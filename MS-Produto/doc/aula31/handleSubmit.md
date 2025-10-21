`src\pages\produtos\novo-produto\index.tsx`

```typescript
const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    // 1. Limpeza de Status e Ativação do Loading de SUBMISSÃO
    setError(null);
    setSuccess(null);
    setIsSubmitting(true); // <--- Usa o isSubmitting para desabilitar o botão

    // 2. Limpa todos os erros de campo antes de começar o novo submit
    setFormErrors({
      nome: null,
      descricao: null,
      valor: null,
      categoriaId: null,
      lojasId: null,
    });

    try {
      const dataToSend = { ...formData };
      // Objeto para pegar erros de Frontend
      const validationErrors: Partial<FormErrors> = {};
      let hasFrontendError = false;
      // Limpa espaços em branco para validação de tamanho/vazio
      const nomeTrim = dataToSend.nome.trim();
      const descricaoTrim = dataToSend.descricao.trim();

      // --- INÍCIO DA VALIDAÇÃO DO FRONTEND ---

      // Validação: Nome
      if (nomeTrim.length === 0) {
        validationErrors.nome = "O campo Nome é obrigatório";
        hasFrontendError = true;
      } else if (nomeTrim.length < 3 || nomeTrim.length > 100) {
        validationErrors.nome =
          "O campo Nome deve ter entre 3 e 100 caracteres";
        hasFrontendError = true;
      }

      // Validação: Descrição
      if (descricaoTrim.length === 0) {
        validationErrors.descricao = "O campo Descrição é obrigatório";
        hasFrontendError = true;
      } else if (descricaoTrim.length < 10) {
        validationErrors.descricao =
          "O campo Descrição deve ter no mínimo 10 caracteres";
        hasFrontendError = true;
      }

      // Validação: Valor
      const valorNumber = Number(dataToSend.valor);
      if (dataToSend.valor === "" || isNaN(valorNumber)) {
        validationErrors.valor =
          "O campo Valor é obrigatório e deve ser um número.";
        hasFrontendError = true;
      } else if (valorNumber <= 0) {
        validationErrors.valor =
          "O campo Valor deve ser um número positivo maior que zero.";
        hasFrontendError = true;
      }

      // Validação: Categoria
      if (dataToSend.categoriaId === "") {
        validationErrors.categoriaId = "Selecione uma Categoria";
        hasFrontendError = true;
      }

      // Validação: Lojas (Array)
      if (dataToSend.lojasId.length === 0) {
        validationErrors.lojasId = "Selecione pelo menos uma Loja";
        hasFrontendError = true;
      }

      // --- FIM DA VALIDAÇÃO DO FRONTEND ---

      // ------------------------------------------------------------------
      // Se houver qualquer erro de validação de frontend, exibe e interrompe o envio
      if (hasFrontendError) {
        setFormErrors((prev) => ({
          ...prev,
          ...validationErrors,
        }));
        // IMPORTANTE: Não precisamos chamar setIsSubmitting(false) aqui se o 'finally' for chamado.
        // No entanto, para garantir que o botão seja reabilitado imediatamente na validação local:
        setIsSubmitting(false);
        return; // Interrompe o submit aqui!
      }

      // 4. MONTAGEM DO DTO FINAL PARA A API (Se a validação local passou)
      const categoriaDTO = { id: Number(dataToSend.categoriaId) };
      const lojasDTO = dataToSend.lojasId.map((id) => ({ id: Number(id) }));

      const createDTO: ProdutoCreateDTO = {
        nome: nomeTrim,
        descricao: descricaoTrim,
        valor: valorNumber,
        categoria: categoriaDTO,
        lojas: lojasDTO,
      };

      // 5. Chamada do Serviço (API) - ONDE O SALVAMENTO OCORRE
      await produtoService.createProduto(createDTO);

      // 6. Sucesso
      setSuccess("Produto cadastrado com sucesso!");

      // Limpa o formulário após o sucesso (opcional)
      setFormData({
        nome: "",
        descricao: "",
        valor: "",
        categoriaId: "",
        lojasId: [],
      });
      setRawValor("");

      setTimeout(() => {
        navigate("/produtos");
      }, 3000);
    } catch (error: unknown) {
      // 7. Tratamento de Erro do Backend
      let msg = "Erro ao cadastrar o Produto. Tente novamente.";

      if (axios.isAxiosError(error) && error.response) {
        const errorData = error.response.data;

        // Tratamento de Erro de Validação de Campo do Backend (Status 422)
        if (
          errorData.status === 422 &&
          errorData.errors &&
          Array.isArray(errorData.errors)
        ) {
          const newErrors: Partial<FormErrors> = {};
          errorData.errors.forEach(
            (err: { field: string; message: string }) => {
              let fieldName = err.field;
              // Mapeamento de campos aninhados do Spring para o estado do Formulário
              if (fieldName.includes("categoria")) {
                fieldName = "categoriaId";
              } else if (fieldName.includes("lojas")) {
                fieldName = "lojasId";
              }

              newErrors[fieldName as keyof FormErrors] = err.message;
              msg = errorData.message || msg;
            }
          );
          setFormErrors((prev) => ({
            ...prev,
            ...newErrors,
          }));
        } else {
          // Tratamento de Erros Gerais (404, 400, etc.)
          msg = errorData.message || errorData.error || msg;
        }
      } else if (error instanceof Error) {
        msg = error.message;
      }

      setError(msg);
      setTimeout(() => setError(null), 4000);
    } finally {
      // 8. FINALIZAÇÃO: Desabilita o estado de submissão
      setIsSubmitting(false);
    }
  };

  // --- useeffect para carregar Categorias e Lojas (Corrigido) ---
  useEffect(() => {
    // Function para carregar as Listas (Definida DENTRO do useEffect)
    const loadDependenciesList = async () => {
      setIsLoading(true); // INICIA o loading de dependências
      setError(null);

      try {
        // Buscas paralelas
        const [categoriaData, lojasData] = await Promise.all([
          categoriaService.findAll(),
          lojasService.findAll(),
        ]);

        setCategorias(categoriaData);
        setLojas(lojasData);
      } catch (error: unknown) {
        let msg = "Erro ao carregar listas de Categorias e Lojas";
        if (axios.isAxiosError(error) && error.response) {
          msg = error.response.data.error || msg;
        }
        // Em caso de erro fatal no carregamento
        setError(msg);
        setTimeout(() => {
          navigate("/produtos", {
            // <--- CORRIGIDO: Adicionado a barra '/'
            state: { globalError: msg },
          });
        }, 3000);
      } finally {
        setIsLoading(false); // FINALIZA o loading (sucesso ou falha)
      }
    };

    // CHAMA A FUNÇÃO para que ela execute na montagem do componente
    loadDependenciesList();
  }, [navigate]);
```
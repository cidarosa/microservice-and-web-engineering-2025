```typescript
// Function para Submit
  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();

    // 1. Limpeza de Status e Ativação do Loading
    setError(null);
    setSuccess(null);
    setIsSubmitting(true);

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
      // Objeto para coletar erros de Frontend
      const validationErrors: Partial<FormErrors> = {};
      let hasFrontendError = false;
      // Limpa espaços em branco para validação de tamanho/vazio
      const nomeTrim = dataToSend.nome.trim();
      const descricaoTrim = dataToSend.descricao.trim();
      // 3. Validações de Frontend
      if (nomeTrim.length === 0) {
        validationErrors.nome = "O campo Nome é obrigatório";
        hasFrontendError = true;
      } else if (nomeTrim.length < 3 || nomeTrim.length > 100) {
        validationErrors.nome =
          "O campo Nome dever ter entre 3 e 100 caracteres";
        hasFrontendError = true;
      }
      if (descricaoTrim.length === 0) {
        validationErrors.descricao = "O campo Descrição é obrigatório";
        hasFrontendError = true;
      } else if (descricaoTrim.length < 10) {
        validationErrors.descricao =
          "O campo Descrição dever ter no mínimo 10 caracteres";
        hasFrontendError = true;
      }

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
      if (dataToSend.categoriaId === "") {
        validationErrors.categoriaId = "Selecione uma Categoria";
        hasFrontendError = true;
      }
      if (dataToSend.lojasId.length === 0) {
        validationErrors.lojasId = "Selecione pelo menos uma Loja";
        hasFrontendError = true;
      }
      // ------------------------------------------------------------------
      // Se houver qualquer erro de validação de frontend, exibe e interrompe o envio
      if (hasFrontendError) {
        setFormErrors((prev) => ({
          ...prev,
          ...validationErrors,
        }));
        setIsSubmitting(false); // Reabilita o botão imediatamente
        return; // Interrompe o submit aqui!
      }

      // 4. MONTAGEM DO DTO FINAL PARA A API
      const categoriaDTO = { id: Number(dataToSend.categoriaId) };
      // Blindando o ID para garantir que seja NUMBER
      const lojasDTO = dataToSend.lojasId.map((id) => ({ id: Number(id) }));
      const updateDTO: ProdutoUpdateDTO = {
        nome: nomeTrim,
        descricao: descricaoTrim,
        valor: valorNumber,
        categoria: categoriaDTO,
        lojas: lojasDTO,
      };

      // 5. Chamada do Serviço (API)
      if (!produtoId) {
        throw Error("ID do Produto não encontrado para atualização.");
      }

      await produtoService.updateProduto(Number(produtoId), updateDTO);

      // 6. Sucesso
      setSuccess("Produto atualizado com sucesso!");
      setTimeout(() => {
        navigate("/produtos");
      }, 3000);
    } catch (error: unknown) {
      let msg = "Erro ao atualizar o Produto. Tente novamente.";

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

              // Mapeamento de campos aninhados do Spring para o estado do Formulário (frontend)
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
        // Tratamento dos erros lançados no Frontend
        msg = error.message;
      }

      setError(msg);
      setTimeout(() => setError(null), 4000);
    } finally {
      setIsSubmitting(false);
    }
  };
```
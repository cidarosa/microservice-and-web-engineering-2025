`src\pages\produtos\editar-produto\index.tsx`

```typescript
useEffect(() => {
    setIsLoading(true);
    setError(null);

    const loadFormData = async () => {
      if (!produtoId) {
        setIsLoading(false);
        setError("Nenhum ID de produto fornecido para edição.");
        return;
      }

      try {
        // 1. Buscas Paralelas: Buscar o produto, categorias e lojas ao mesmo tempo
        const [produtoData, categoriasData, lojasData] = await Promise.all([
          produtoService.findById(Number(produtoId)),
          categoriaService.findAll(),
          lojaService.findAll(),
        ]);
        // 2. Definindo os estados
        setCategorias(categoriasData);
        setLojas(lojasData);
        // 3. Preencher o formulário com dados do produto
        setFormData({
          nome: produtoData.nome,
          descricao: produtoData.descricao,
          valor: produtoData.valor,
          categoriaId: produtoData.categoria.id,
          // Mapeia a lista de objetos LojaDTO para uma lista de IDs
          lojasId: produtoData.lojas.map((loja) => loja.id),
        });

        // =======================================================
        // Sincroniza o estado de exibição (string) com o valor do backend (number)
        const valorDoBackend = produtoData.valor;

        // Converte o number do backend para string, tratando valores nulos/vazios
        const valorInicialString = valorDoBackend ? String(valorDoBackend) : "";

        // ATUALIZA O ESTADO AUXILIAR que é usado no TextField
        setRawValor(valorInicialString);
        
        // =======================================================
      } catch (error: unknown) {
        let msg = "Erro ao carregar dados do Produto";
        if (axios.isAxiosError(error) && error.response) {
          msg = error.response.data.error || msg;
        }
        setError(msg);
        setTimeout(() => setError(null), 4000);
      } finally {
        setIsLoading(false);
      }
    };
    loadFormData();
  }, [produtoId]);
```

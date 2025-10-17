`src\pages\produtos\editar-produto\index.tsx`

```typescript
// Function para manipular mudanças no form
  const handleChange = (
    e: ChangeEvent<HTMLInputElement | HTMLTextAreaElement> | SelectChangeEvent
  ) => {
    const { name, value } = e.target;
    if (name === "valor") {
      // 1. FILTRO: Permite dígitos, vírgula e ponto para a digitação livre.
      const rawInput = value.replace(/[^\d,.]/g, "");

      // 2. ATUALIZA O ESTADO DE EXIBIÇÃO: Isto garante 
      // que a string digitada (150,5) permaneça.
      setRawValor(rawInput);

      // ADICIONE O RETURN AQUI para garantir que não caia na lógica padrão
      return;
    } else if (name === "categoriaId") {
      // O Select retorna o ID como string.
      // Armazenamos como Number (se houver valor)
      // ou string vazia (para o placeholder).
      const selectValue = value === "" ? "" : Number(value);
      setFormData((prevData) => ({
        ...prevData,
        [name]: selectValue,
      }));
    } else if (name === "lojasId") {
      let newLojasId: number[] = [];
      // O valor (value) pode ser uma string (se for uma única seleção)
      // ou um array (se for multi-select)
      // Para garantir que sempre temos um array
      //  NOTA: O value vem como `unknown` aqui e precisa ser tratado como array.
      const selectedValues = Array.isArray(value) ? value : [value];
      // Mapeando os IDs para números, filtrando valores vazios se houver
      newLojasId = selectedValues
        .map((id) => Number(id))
        .filter((id) => !isNaN(id)); // Garante que só Numbers válidos no array
      setFormData((prevData) => ({
        ...prevData,
        [name]: newLojasId,
      }));
    } else {
      // Para os demais campos de texto (nome, descricao)
      setFormData((prevData) => ({
        ...prevData,
        [name]: value,
      }));
    }
  };

```
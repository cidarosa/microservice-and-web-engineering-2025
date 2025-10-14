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

  // Adicionar esta função logo abaixo do handleChange:
  // NOVA FUNÇÃO: Chamada ao sair do campo para limpar e salvar o número no formData
  const handleBlurValor = () => {
    // Pega a string crua que o usuário digitou (ex: "150,5")
    const valorDigitado = rawValor;

    if (!valorDigitado) {
      // Se vazio, limpa os dois estados
      setRawValor("");
      setFormData((prevData) => ({ ...prevData, valor: "" }));
      return;
    }

    // 1. Limpa e converte a string para o número final (ex: 150.5)
    // Usa sua função unmaskCurrency:
    const numericValue = unmaskCurrency(valorDigitado);

    // 2. Garante que salvamos o valor numérico final (ou "" se for zero)
    const finalValue = numericValue === 0 ? "" : numericValue;

    // =======================================================
    // 3. Atualiza o estado de exibição (rawValor) com a formatação BRL

    let stringFormatada: string;
    if (finalValue !== "") {
      // Formata o número limpo (150.5) de volta para a string visual ("150,50")
      stringFormatada = formatToBRL(finalValue);
    } else {
      // Limpa o estado de exibição se o valor final for zero ou vazio
      stringFormatada = "";
    }

    // ATUALIZA O ESTADO DE EXIBIÇÃO
    setRawValor(stringFormatada);
    // =======================================================

    // 4. Atualiza o estado PRINCIPAL (o número limpo para envio)
    setFormData((prevData) => ({
      ...prevData,
      valor: finalValue, // finalValue é o number limpo (150.5)
    }));
  };
```
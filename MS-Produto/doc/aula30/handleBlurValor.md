```typescript
// FUNÇÃO: Chamada ao sair do campo para limpar e salvar o número no formData
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
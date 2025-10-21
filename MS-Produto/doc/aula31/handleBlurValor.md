`src\pages\produtos\novo-produto\index.tsx`

```typescript
const handleBlurValor = () => {
    
    const valorDigitado = rawValor;

    if (!valorDigitado) {
      setRawValor("");
      setFormData((prevData) => ({ ...prevData, valor: "" }));
      return;
    }
    const numericValue = unmaskCurrency(valorDigitado);

    const finalValue = numericValue === 0 ? "" : numericValue;

    let stringFormatada: string;
    if (finalValue !== "") {
      stringFormatada = formatToBRL(finalValue);
    } else {
      stringFormatada = "";
    }
    setRawValor(stringFormatada);
    setFormData((prevData) => ({
      ...prevData,
      valor: finalValue,
    }));
  };
```
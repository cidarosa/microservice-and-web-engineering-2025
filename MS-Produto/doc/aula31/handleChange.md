`src\pages\produtos\novo-produto\index.tsx`
```typescript
const handleChange = (
    e: ChangeEvent<HTMLInputElement | HTMLTextAreaElement> | SelectChangeEvent
  ) => {
    const { name, value } = e.target;
    
    // 1. Lógica para o Campo VALOR (usa o state auxiliar rawValor)
    if (name === "valor") {
      const rawInput = value.replace(/[^\d,.]/g, "");
      setRawValor(rawInput);
      return;
    }
    // 2. Lógica para o Select de Categoria
    else if (name === "categoriaId") {
      const selectValue = value === "" ? "" : Number(value);
      setFormData((prevData) => ({
        ...prevData,
        [name]: selectValue,
      }));
    }
    // 3. Lógica para o Multi-Select de Lojas
    else if (name === "lojasId") {
      let newLojasId: number[] = [];
      const selectedValues = Array.isArray(value) ? value : [value];

      newLojasId = selectedValues
        .map((id) => Number(id))
        .filter((id) => !isNaN(id));
      setFormData((prevData) => ({
        ...prevData,
        [name]: newLojasId,
      }));
    }
    // 4. Lógica para os demais campos de texto (nome, descricao)
    else {
      setFormData((prevData) => ({
        ...prevData,
        [name]: value,
      }));
    }
  };
```
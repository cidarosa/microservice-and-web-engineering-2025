`src\pages\produtos\novo-produto\index.tsx`

```typescript
// --- RENDERIZAÇÃO (JSX) ---
  return (
    <Box sx={{ mt: 2, p: 4 }}>
      {/* MENSAGENS GLOBAIS DE SUCESSO/ERRO (Backend/Geral) */}
      {success && (
        <Alert severity="success" sx={{ mb: 2 }}>
          {success}
        </Alert>
      )}

      {error && (
        <Alert severity="error" sx={{ mb: 2 }}>
          {error}
        </Alert>
      )}

      <Typography variant="h4" component="h1">
        Cadastrar Produto
      </Typography>

      {/* 1. EXIBIÇÃO DE LOADING INICIAL */}
      {isLoading ? (
        <Box sx={{ display: "flex", justifyContent: "center", mt: 4 }}>
          <CircularProgress />
        </Box>
      ) : (
        // 2. EXIBIÇÃO DO FORMULÁRIO (Somente se não houver erro inicial)
        !error && (
          <Box
            component="form"
            onSubmit={handleSubmit}
            noValidate
            sx={{ mt: 1 }}
          >
            {/* 1. CAMPO NOME*/}
            <TextField
              margin="normal"
              required
              fullWidth
              id="nome"
              label="Nome do Produto"
              name="nome"
              autoFocus
              value={formData.nome}
              onChange={handleChange}
              // onBlur para trimar espaços (boa prática)
              onBlur={() => {
                const nomeAtual = formData.nome;
                const nomeTrimado = nomeAtual.trim();
                if (nomeAtual !== nomeTrimado) {
                  setFormData((prevData) => ({
                    ...prevData,
                    nome: nomeTrimado,
                  }));
                }
              }}
              error={!!formErrors.nome}
              helperText={formErrors.nome}
              disabled={isSubmitting}
              sx={{ mb: 2 }}
            />

            {/* 2. CAMPO DESCRIÇÃO */}
            <TextField
              margin="normal"
              required
              fullWidth
              multiline
              rows={3}
              id="descricao"
              label="Descrição do Produto"
              name="descricao"
              value={formData.descricao}
              onChange={handleChange}
              onBlur={() => {
                const descricaoAtual = formData.descricao;
                const descricaoTrimado = descricaoAtual.trim();
                if (descricaoAtual !== descricaoTrimado) {
                  setFormData((prevData) => ({
                    ...prevData,
                    descricao: descricaoTrimado,
                  }));
                }
              }}
              error={!!formErrors.descricao}
              helperText={formErrors.descricao}
              disabled={isSubmitting}
              sx={{ mb: 2 }}
            />

            {/* 3. CAMPO VALOR */}
            <TextField
              margin="normal"
              required
              fullWidth
              id="valor"
              label="Valor do Produto"
              name="valor"
              value={rawValor} // <<< USA O ESTADO DE STRING AUXILIAR
              onChange={handleChange}
              onBlur={handleBlurValor} // <<< CHAMA A LIMPEZA E SALVAMENTO FINAL
              error={!!formErrors.valor}
              helperText={formErrors.valor}
              disabled={isSubmitting}
              InputProps={{
                startAdornment: (
                  <InputAdornment position="start">R$</InputAdornment>
                ),
              }}
              sx={{ mb: 2 }}
            />

            {/* 4. Select para Categoria */}
            <FormControl
              fullWidth
              margin="normal"
              required
              sx={{ mb: 2 }}
              error={!!formErrors.categoriaId}
              disabled={isSubmitting}
            >
              <InputLabel id="categoria-label">Categoria</InputLabel>
              <Select
                labelId="categoria-label"
                id="categoriaId"
                name="categoriaId"
                value={
                  formData.categoriaId === ""
                    ? ""
                    : String(formData.categoriaId)
                }
                label="Categoria"
                onChange={handleChange}
              >
                <MenuItem value="">
                  <em>Selecione uma Categoria</em>
                </MenuItem>
                {categorias.map((cat) => (
                  <MenuItem key={cat.id} value={cat.id}>
                    {cat.nome}
                  </MenuItem>
                ))}
              </Select>
              {formErrors.categoriaId && (
                <Typography
                  variant="caption"
                  color="error"
                  sx={{ ml: 2, mt: 0.5 }}
                >
                  {formErrors.categoriaId}
                </Typography>
              )}
            </FormControl>

            {/* 5. Multi-Select para Lojas */}
            <FormControl
              fullWidth
              margin="normal"
              required
              sx={{ mb: 2 }}
              error={!!formErrors.lojasId}
              disabled={isSubmitting}
            >
              <InputLabel id="lojas">Lojas</InputLabel>
              <Select
                labelId="lojas"
                id="lojas-multiple-chip"
                multiple
                name="lojasId"
                value={formData.lojasId}
                // Usamos uma função anônima para garantir a tipagem correta
                onChange={(event) => handleChange(event as SelectChangeEvent)}
                label="Lojas"
                renderValue={(selectedIds: number[]) => (
                  <Box sx={{ display: "flex", flexWrap: "wrap", gap: 0.5 }}>
                    {selectedIds.map((id) => {
                      const loja = lojas.find((l) => l.id === id);
                      return (
                        <Chip key={id} label={loja ? loja.nome : `ID ${id}`} />
                      );
                    })}
                  </Box>
                )}
              >
                {lojas.map((loja) => (
                  <MenuItem key={loja.id} value={loja.id}>
                    {loja.nome}
                  </MenuItem>
                ))}
              </Select>
              {formErrors.lojasId && (
                <Typography
                  variant="caption"
                  color="error"
                  sx={{ ml: 2, mt: 0.5 }}
                >
                  {formErrors.lojasId}
                </Typography>
              )}
            </FormControl>

            <Box
              sx={{
                display: "flex",
                justifyContent: "flex-end",
                gap: 2,
                mt: 3,
              }}
            >
              <Button
                variant="outlined"
                size="large"
                onClick={() => navigate("/produtos")}
                disabled={isSubmitting}
              >
                Cancelar
              </Button>
              <Button
                type="submit"
                size="large"
                variant="contained"
                disabled={isSubmitting} // Desabilita o botão durante o submit
              >
                {isSubmitting ? <CircularProgress size={24} /> : "Salvar"}
              </Button>
            </Box>
          </Box>
        )
      )}
    </Box>
  );
```
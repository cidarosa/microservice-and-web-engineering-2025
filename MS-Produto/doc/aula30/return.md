```typescript
return (
    <Box sx={{ mt: 2, p: 4 }}>
      {/* MENSAGENS GLOBAIS DE SUCESSO/ERRO */}
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
        Editar Produto
      </Typography>

      {isLoading ? (
        <Box sx={{ display: "flex", justifyContent: "center", mt: 4 }}>
          <CircularProgress />
        </Box>
      ) : (
        !error && (
          /* 3. FORMULÁRIO (SÓ APARECE SE NÃO ESTIVER CARREGANDO NEM TIVER ERRO INICIAL) */
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
              onBlur={() => {
                // 1. Pega o valor atual do estado (que pode ter espaços)
                const nomeAtual = formData.nome;
                const nomeTrimado = nomeAtual.trim();

                // 2. Verifica se houve alteração e atualiza o estado se necessário
                if (nomeAtual !== nomeTrimado) {
                  setFormData((prevData) => ({
                    ...prevData,
                    nome: nomeTrimado, // Salva o valor trimado de volta no formData
                  }));
                }
              }}
              // 1. ATIVA O ESTILO DE ERRO (BORDA VERMELHA)
              error={!!formErrors.nome}
              // 2. EXIBE A MENSAGEM DE ERRO (HELPER TEXT)
              helperText={formErrors.nome}
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
                // 1. Pega o valor atual do estado (que pode ter espaços)
                const descricaoAtual = formData.descricao;
                const descricaoTrimado = descricaoAtual.trim();

                // 2. Verifica se houve alteração e atualiza o estado se necessário
                if (descricaoAtual !== descricaoTrimado) {
                  setFormData((prevData) => ({
                    ...prevData,
                    descricao: descricaoTrimado, // Salva o valor trimado de volta no formData
                  }));
                }
              }}
              error={!!formErrors.descricao}
              helperText={formErrors.descricao}
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
              slotProps={{
                input: {
                  startAdornment: (
                    <InputAdornment position="start">R$</InputAdornment>
                  ),
                },
              }}
              sx={{ mb: 2 }}
            />

            {/* 4. Select para Categoria */}
            <FormControl
              fullWidth
              margin="normal"
              required
              sx={{ mb: 2 }}
              // <<< Conecta o estado de erro ao FormControl >>>
              error={!!formErrors.categoriaId}
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
                } // Converte para string para o Select
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
              {/* <<< Exibe a mensagem de erro (helper text) >>> */}
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

            {/* 5.  Multi-Select para Lojas */}
            <FormControl
              fullWidth
              margin="normal"
              required
              sx={{ mb: 2 }}
              // <<< Conecta o estado de erro ao FormControl >>>
              error={!!formErrors.lojasId}
            >
              <InputLabel id="lojas">Lojas</InputLabel>
              <Select
                labelId="lojas"
                id="lojas-multiple-chip"
                multiple
                name="lojasId"
                // O VALOR é o array de números (number[]) do seu state
                value={formData.lojasId}
                // SOLUÇÃO DE TIPAGEM: Usamos uma função anônima que recebe o evento tipado
                // e passa para o handleChange, que aceita o SelectChangeEvent.
                onChange={(event) => handleChange(event as SelectChangeEvent)}
                label="Lojas Associadas"
                // renderValue é tipado com number[] para funcionar com o value
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
                  // O valor do MenuItem também é numérico (number)
                  <MenuItem key={loja.id} value={loja.id}>
                    {loja.nome}
                  </MenuItem>
                ))}
              </Select>
              {/* <<< Exibe a mensagem de erro (helper text) >>> */}
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

            <Button
              type="submit"
              size="large"
              variant="contained"
              sx={{ mt: 3, mb: 2 }}
              disabled={isSubmitting} // Desabilita o botão durante o submit
            >
              {isSubmitting ? <CircularProgress size={24} /> : "Salvar"}
            </Button>
          </Box>
        )
      )}
    </Box>
  );
```
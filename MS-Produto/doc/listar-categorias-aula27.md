```typescript
/* eslint-disable @typescript-eslint/no-unused-vars */
import {
  Box,
  CircularProgress,
  IconButton,
  Paper,
  Table,
  TableBody,
  TableCell,
  TableContainer,
  TableHead,
  TableRow,
  Typography,
} from "@mui/material";

import type { CategoriaDTO } from "../../../models/categoria";
import { useEffect, useState } from "react";

import * as categoriaService from "../../../services/categoria-service";
import { Link } from "react-router-dom";
import { Delete, Edit } from "@mui/icons-material";

export default function ListarCategorias() {
  const [categorias, setCategorias] = useState<CategoriaDTO[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchCategorias = async () => {
      try {
        const data = await categoriaService.findAll();
        setCategorias(data);
      } catch (error: unknown) {
        if (error instanceof Error) {
          setError(error.message);
        } else {
          setError("Ocorreu um erro desconhecido");
        }
      } finally {
        setLoading(false);
      }
    };
    fetchCategorias();
  }, []);

  return (
    <Box sx={{ p: 4 }}>
      <Typography variant="h4" component="h1" gutterBottom>
        Página de Listagem de Categorias
      </Typography>
      {/* Renderização condicional */}
      {loading ? (
        <Box sx={{ display: "flex", justifyContent: "center", mt: 4 }}>
          <CircularProgress />
        </Box>
      ) : error ? (
        <Typography color="error" sx={{ mt: 4 }}>
          {error}
        </Typography>
      ) : (
        <Typography variant="body1">
          <TableContainer component={Paper}>
            <Table sx={{ minWidth: 650 }} aria-label="simple table">
              <TableHead>
                <TableRow>
                  <TableCell>ID</TableCell>
                  <TableCell>Nome</TableCell>
                  <TableCell>Ações</TableCell>
                </TableRow>
              </TableHead>
              <TableBody>
                {/* Carrega os dados na tabela */}
                {categorias.map((categoria) => (
                  <TableRow key={categoria.id}>
                    <TableCell>{categoria.id}</TableCell>
                    <TableCell>{categoria.nome}</TableCell>
                    <TableCell>
                      <IconButton
                        aria-label="editar"
                        component={Link}
                        to={`/categorias/${categoria.id}/editar`}
                      >
                        <Edit />
                      </IconButton>
                      <IconButton
                        aria-label="excluir"
                        onClick={() =>
                          console.log("Excluir categoria: ", categoria.id)
                        }
                        sx={{ ml: 1 }}
                      >
                        <Delete />
                      </IconButton>
                    </TableCell>
                  </TableRow>
                ))}
              </TableBody>
            </Table>
          </TableContainer>
        </Typography>
      )}
    </Box>
  );
}

```
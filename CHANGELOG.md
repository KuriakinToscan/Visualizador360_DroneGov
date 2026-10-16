# Histórico de Versões

Todas as mudanças relevantes do Visualizador 360° DroneGov.

## [1.1] — 2026-10-16

### Motor de renderização
- **Shader esférico calculado pixel a pixel** (GLSL), que elimina a distorção no Nadir (solo abaixo do drone) e as costuras nos polos.
- Filtragem linear sem mipmaps, rotação da câmera na ordem `YXZ` e inclinação de -89,9° a +89,9°.

### Novidades
- **Padrões institucionais DroneGov:** título `DRONEGOV` e subtítulo `Associação Brasileira de Aviação Pública Não Tripulada e Sistemas Embarcados`.
- **Escolha dos metadados exibidos:** modelo do drone, data/hora e coordenadas GPS podem ser incluídos ou não no visualizador.
- Crédito "Desenvolvido por Kuriakin Toscan" no rodapé do gerador.

### 100% offline e arquivos menores
- **Visualizadores gerados funcionam sem internet:** o Three.js r128 passa a ser embutido no arquivo (antes vinha de CDN e, sem rede, a tela ficava preta).
- **Gerador funciona sem internet:** biblioteca EXIF, ícones e fontes embutidos ou locais.
- **Logotipo DroneGov embutido uma única vez** (WebP de ~139 KB, no lugar de um SVG de 6 MB repetido 4 vezes): o gerador passa de 24 MB para ~850 KB.
- **Panorâmica embutida uma única vez** no visualizador gerado. Com uma foto de 3,8 MB, o visualizador passa de 16 MB para 5,8 MB, dentro do limite do SEI.

### Correções
- Textos com acentos apareciam como códigos (`ç...`) nos campos de título e subtítulo.
- Campos de título e subtítulo sem o estilo dos demais campos.

### Repositório e documentação
- `LogoDroneGov.svg` removido (era uma imagem PNG embrulhada em SVG); `LogoDroneGov.png` movido para `assets/`.
- Arquivos temporários e logotipos duplicados removidos.
- README reescrito, documentação técnica revisada e este histórico de versões criado.

## [1.0]

Lançamento oficial do Visualizador 360° DroneGov.

- Rodapé do gerador com link para o repositório e licença MIT.
- Documentação técnica detalhada (`DOCUMENTACAO_TECNICA.md`).

## [0.9]

Versão inicial: gerador de visualizadores 360° em HTML autônomo, leitura de metadados EXIF, descrição do local e personalização da marca.

[1.1]: https://github.com/KuriakinToscan/Visualizador360_DroneGov/releases/tag/v1.1
[1.0]: https://github.com/KuriakinToscan/Visualizador360_DroneGov/releases/tag/v1.0
[0.9]: https://github.com/KuriakinToscan/Visualizador360_DroneGov/releases/tag/v0.9

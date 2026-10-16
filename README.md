# Visualizador 360° DroneGov — Versão 1.1

[![Versão](https://img.shields.io/github/v/release/KuriakinToscan/Visualizador360_DroneGov?label=vers%C3%A3o&color=102a45)](https://github.com/KuriakinToscan/Visualizador360_DroneGov/releases/latest)
[![Licença MIT](https://img.shields.io/badge/licen%C3%A7a-MIT-2e7d46)](LICENSE)
![100% offline](https://img.shields.io/badge/100%25-offline-d89e2b)

> **Transforme fotos panorâmicas 360° de drone em relatórios interativos para o SEI.**
> Um único arquivo HTML gera visualizadores 3D autônomos, com metadados da foto e a marca da sua instituição, que abrem em qualquer navegador e funcionam sem internet.
>
> **Powered by DroneGov.**

### ⬇️ [Baixar a versão mais recente](https://github.com/KuriakinToscan/Visualizador360_DroneGov/releases/latest)
Na página da versão, baixe o arquivo **`360_Gerador.html`** em *Assets* e abra com duplo clique. Não precisa instalar nada.

---

## 📌 Sumário
- [1. O que é](#1-o-que-é)
- [2. Funcionalidades](#2-funcionalidades)
- [3. Requisitos](#3-requisitos)
- [4. Como gerar um visualizador](#4-como-gerar-um-visualizador)
- [5. Como incluir no SEI](#5-como-incluir-no-sei)
- [6. Tamanho dos arquivos gerados](#6-tamanho-dos-arquivos-gerados)
- [7. Usando o visualizador](#7-usando-o-visualizador)
- [8. Personalização da marca](#8-personalização-da-marca)
- [9. Solução de problemas](#9-solução-de-problemas)
- [10. Estrutura do repositório](#10-estrutura-do-repositório)
- [11. Autoria e créditos](#11-autoria-e-créditos)
- [12. Histórico de versões e licença](#12-histórico-de-versões-e-licença)

---

## 1. O que é

O **Visualizador 360° DroneGov** converte uma fotografia panorâmica esférica (equirretangular, proporção 2:1), capturada por drone ou câmera 360°, em um **arquivo HTML autônomo** com visualização 3D interativa.

O arquivo gerado reúne a foto, os metadados escolhidos (modelo do drone, data/hora e coordenadas GPS), uma descrição livre e a identidade visual da instituição. Ele foi pensado para ser anexado a processos do **SEI (Sistema Eletrônico de Informações)** e aberto diretamente no navegador, sem programas adicionais e **sem conexão com a internet**.

Tanto o gerador quanto os visualizadores gerados processam tudo localmente: nenhuma foto ou metadado é enviado para servidores externos.

---

## 2. Funcionalidades

* 🌐 **100% offline:** o gerador e os visualizadores gerados têm todas as bibliotecas, ícones e imagens embutidos. Nenhum recurso é baixado da internet.
* 🧭 **Visualização 3D imersiva:** Three.js com shader esférico calculado pixel a pixel, sem distorção no Nadir (solo logo abaixo do drone) nem costuras nos polos; inclinação de -89,9° a +89,9°.
* 🛰️ **Leitura automática de metadados EXIF:** modelo do drone/câmera, data e hora da captura e coordenadas GPS em graus decimais.
* ✅ **Escolha do que exibir:** cada metadado pode ser incluído ou não no cabeçalho do visualizador, além de uma descrição livre do local ou do voo.
* 🛡️ **Marca institucional personalizável:** logotipo, título e subtítulo do cabeçalho, salvos no navegador para os próximos relatórios. Padrão: DroneGov.
* 📦 **Arquivos compactos:** o visualizador gerado tem cerca de 1,33 vez o tamanho da foto, mais ~0,9 MB fixos (seção 6).
* 🔤 **Acentuação garantida:** o arquivo é salvo em UTF-8 com BOM, para exibir corretamente os acentos no SEI e em navegadores embutidos no Windows.
* ❓ **Ajuda embutida:** botão **Ajuda / Instruções** com o passo a passo dentro do próprio gerador.

---

## 3. Requisitos

- Navegador moderno com **WebGL**: Google Chrome, Microsoft Edge, Mozilla Firefox ou Brave (atualizados).
- Computador com mouse: o gerador e o visualizador são operados com o mouse (não há suporte a gestos de toque nesta versão).
- Nenhum servidor, instalação ou conexão com a internet.

---

## 4. Como gerar um visualizador

1. Abra o **`360_Gerador.html`** no navegador (duplo clique).
2. **Marca (opcional):** ajuste o **Título Principal**, o **Subtítulo / Órgão** e, se quiser, troque o logotipo em **Alterar Logo (Upload)** (seção 8).
3. **Foto:** arraste a panorâmica para a área indicada ou clique para selecionar o arquivo (JPG, PNG ou WebP, de preferência na proporção 2:1). O gerador mostra a pré-visualização, as dimensões, a proporção e os metadados lidos.
4. **Metadados a exibir:** marque ou desmarque **Modelo do Drone**, **Data e Hora** e **Coordenadas GPS**.
5. **Nome e descrição:** confira o **Nome do arquivo de saída** e, se quiser, preencha a **Descrição do Local/Voo**.
6. Clique em **Gerar Visualizador** e depois em **Baixar HTML**.
7. Para outra foto, clique em **Processar outra imagem**.

---

## 5. Como incluir no SEI

1. No processo, clique em **Incluir Documento** e escolha **Externo**.
2. Em **Formato**, selecione **Nato-digital**.
3. Em **Tipo de Conferência**, escolha a opção adequada (por exemplo, *Documento Original*).
4. Anexe o arquivo `.html` gerado.
5. Ao abrir o documento na árvore do processo, o SEI exibe a visualização 360° interativa no navegador.

---

## 6. Tamanho dos arquivos gerados

O tamanho do visualizador depende principalmente da foto:

> **Tamanho do visualizador ≈ 1,33 × tamanho da foto + 0,9 MB**
>
> (1,33 é o acréscimo da codificação base64; os 0,9 MB fixos incluem o motor 3D e o logotipo.)

| Foto panorâmica | Visualizador gerado (aprox.) |
| :---: | :---: |
| 2 MB | 3,6 MB |
| 3 MB | 4,9 MB |
| 4 MB | 6,2 MB |
| 6 MB | 8,9 MB |

O limite de upload do SEI costuma ficar entre **5 MB e 10 MB**, conforme a instalação. Se o arquivo ficar grande demais, reduza a foto para **4096 × 2048 px** ou aumente levemente a compressão JPEG antes de gerar. Um logotipo personalizado pesado também aumenta o arquivo.

---

## 7. Usando o visualizador

- Clique em **▶ Abrir visualização 360°**.
- **Arrastar com o mouse:** gira a vista (com movimento suavizado).
- **Roda do mouse** ou botões **+ / −:** zoom (campo de visão de 30° a 110°).
- **▶ (rotação automática):** liga ou desliga o giro contínuo da vista.
- **⟳:** volta à vista inicial (campo de visão de 75°).
- A **bússola** no canto superior direito acompanha a direção horizontal da vista.

---

## 8. Personalização da marca

| Item | Padrão | Como alterar |
| :--- | :--- | :--- |
| Logotipo | DroneGov | **Alterar Logo (Upload)**: PNG, SVG, JPG ou WebP de até 4 MB |
| Título principal | `DRONEGOV` | Campo **Título Principal** |
| Subtítulo | `Associação Brasileira de Aviação Pública Não Tripulada e Sistemas Embarcados` | Campo **Subtítulo / Órgão** |

- As escolhas ficam salvas no navegador (`localStorage`) e são reaproveitadas nos próximos relatórios.
- **Restaurar Padrões** volta ao logotipo, título e subtítulo DroneGov.
- O selo **"Powered by DroneGov"** no rodapé do visualizador é fixo.

---

## 9. Solução de problemas

* **A marca antiga continua aparecendo depois de atualizar o gerador:** as preferências salvas no navegador têm prioridade. Clique em **Restaurar Padrões**.
* **O logotipo não é salvo:** o espaço de armazenamento do navegador é limitado. Use uma imagem menor (algumas centenas de KB são suficientes).
* **Imagem distorcida:** confirme que a foto é uma panorâmica esférica equirretangular, na proporção 2:1. O gerador avisa quando a proporção é diferente.
* **Arquivo grande demais para o SEI:** veja a seção 6.
* **Tela preta no visualizador:** o navegador precisa de WebGL. Atualize o navegador ou verifique se a aceleração gráfica não está desativada nas configurações.
* **Metadados "Não disponível":** a foto não traz essas informações no EXIF (comum em imagens editadas ou exportadas por alguns programas).

---

## 10. Estrutura do repositório

| Arquivo | Descrição |
| :--- | :--- |
| `360_Gerador.html` | O aplicativo: gerador de visualizadores 360°, com todas as bibliotecas e o logotipo embutidos |
| `README.md` | Este guia |
| `DOCUMENTACAO_TECNICA.md` | Documentação técnica: arquitetura, metadados, motor 3D e integração com o SEI |
| `CHANGELOG.md` | Histórico de versões |
| `assets/LogoDroneGov.png` | Arquivo de origem do logotipo DroneGov (2130 × 1984 px) |
| `LICENSE` | Licença MIT |

---

## 11. Autoria e créditos

* **Autor e idealizador original:** Gilberto Milhomem Marinho Filho.
* **Evolução de interface, motor de renderização e versão 1.1:** Kuriakin Toscan.
* **Metadados EXIF, personalização de marca e descrição:** Luiz Augusto ([github.com/laoc81](https://github.com/laoc81)).

**Bibliotecas embutidas (todas sob licença MIT):**
* [Three.js](https://threejs.org) r128 — © 2010-2021 Three.js Authors.
* [EXIF-js](https://github.com/exif-js/exif-js) 2.3.0 — © Jacob Seidelin.
* [Phosphor Icons](https://phosphoricons.com) — © Phosphor Icons.

---

## 12. Histórico de versões e licença

* As mudanças de cada versão estão no [`CHANGELOG.md`](CHANGELOG.md), e as versões publicadas, com o arquivo para download, em [Releases](https://github.com/KuriakinToscan/Visualizador360_DroneGov/releases).
* Código distribuído sob a **[Licença MIT](LICENSE)**. O logotipo DroneGov é marca do projeto DroneGov.

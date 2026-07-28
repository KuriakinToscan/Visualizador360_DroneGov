# Visualizador 360º — DroneGov

Visualizador e gerador de relatórios imersivos em 360° com extração de metadados EXIF e suporte a personalização de marca institucional, desenvolvido para integração com sistemas de processos eletrônicos (como o SEI - Sistema Eletrônico de Informações).

---

## ⚖️ Nota de Autoria e Evolução

* **Autor e Idealizador Original:** Gilberto Milhomem Marinho Filho.
* **Modificações e Evolução de Interface (2026):** Kuriakin Toscan.
* **Aprimoramento e Novas Funcionalidades (EXIF, Personalização de Marca, Descrição, 2026):** Luiz Augusto - ([github.com/laoc81](https://github.com/laoc81)).
* **Instituição:** IBAMA — Instituto Brasileiro do Meio Ambiente e dos Recursos Naturais Renováveis.
* **Unidade de Origem:** DIPAM-TO — Divisão de Proteção Ambiental do Tocantins.

---

## 🎯 Objetivo

Permitir a visualização e documentação imersiva de imagens 360° capturadas por drones diretamente em ambientes de processos administrativos (como o SEI), convertendo fotografias panorâmicas em arquivos HTML autônomos e autocontidos (via Base64). Os arquivos gerados podem ser abertos em qualquer navegador sem necessidade de conexão com a internet ou softwares externos.

---

## 🛠️ Características Técnicas e Funcionalidades

* **Arquitetura Autônoma (Zero Dependências Externas):** O arquivo HTML final possui todos os recursos (imagens, scripts, folhas de estilo e metadados) embutidos em Base64, funcionando 100% offline.
* **Motor de Renderização 3D:** Utiliza a biblioteca **Three.js (r128)** para projeção esférica tridimensional via WebGL com aceleração por hardware.
* **Navegação Suave e Controle Imersivo:** Suporte a rotação por arraste (com interpolação suave *lerp*), rotação automática (*auto-rotate*), controle de Zoom/FOV (Field of View) e redefinição de vista.
* **Personalização de Marca Institucional (Persistente):**
  * **Logotipo Customizável:** Upload da marca da instituição/órgão (PNG, SVG, JPG, WebP) ou utilização do logotipo padrão DroneGov.
  * **Título e Subtítulo Editáveis:** Definição do título principal (padrão: `VISUALIZADOR 360º`) e subtítulo (padrão: `COM METADADOS`) exibidos no cabeçalho.
  * **Persistência Local:** As preferências de marca são salvas automaticamente no `localStorage` do navegador para utilização em futuras gerações de relatórios.
* **Extração Automática de Metadados EXIF:** Integração com a biblioteca `exif-js` para leitura automática dos dados armazenados na fotografia:
  * Modelo do drone/câmera;
  * Data e hora da captura;
  * Coordenadas GPS (Latitude e Longitude) com formatação em graus sexagesimais;
  * Dimensões e proporção da imagem (recomendado 2:1 panorâmico).
* **Campo de Descrição Personalizada:** Inclusão de observações, notas de fiscalização ou contexto da inspeção no relatório final.

---

## 🚀 Como Utilizar

### 1. Gerar o Visualizador 360° (`360_Gerador.html`)

1. Abra o arquivo [360_Gerador.html](file:///d:/Visualizador360_DroneGov/360_Gerador.html) em qualquer navegador moderno (Google Chrome, Microsoft Edge, Firefox, Brave).
2. **(Opcional) Configurar a Marca Institucional:**
   * Clique em **"Alterar Logo (Upload)"** para selecionar o arquivo de imagem da sua instituição (máx. 4MB).
   * Altere os campos **"Título do Cabeçalho"** e **"Subtítulo"** conforme a necessidade da sua unidade.
   * Se desejar retornar às configurações padrão, clique em **"Restaurar Padrão"**.
3. **Carregar a Imagem 360°:**
   * Arraste a imagem panorâmica esférica (proporção 2:1) para a área de soltura ou clique em **"Selecionar Arquivo"**.
4. **Verificar os Metadados & Descrição:**
   * Confira as informações lidas da imagem (Modelo, Data/Hora, Coordenadas GPS).
   * Preencha o campo **"Descrição / Observações"** com as notas técnicas ou de inspeção relevantes.
5. **Gerar e Baixar:**
   * Clique em **"Gerar Visualizador 360°"** e salve o arquivo HTML gerado.

---

### 2. Anexar e Visualizar no SEI

1. No processo administrativo do SEI, clique em **"Incluir Documento"** e selecione a opção **"Externo"**.
2. No campo **"Formato"**, selecione **"Nato-digital"**.
3. No campo **"Tipo de Conferência"**, selecione a opção adequada (ex: *Documento Original*).
4. Faça o upload do arquivo HTML gerado.
5. Ao selecionar o documento na árvore do processo, o SEI abrirá a visualização panorâmica interativa 360° diretamente no navegador interno.

---

## 📁 Estrutura do Repositório

| Arquivo | Descrição |
| :--- | :--- |
| [360_Gerador.html](file:///d:/Visualizador360_DroneGov/360_Gerador.html) | Aplicação web principal para configuração de marca, leitura de EXIF e geração do arquivo HTML 360° final. |
| [README.md](file:///d:/Visualizador360_DroneGov/README.md) | Documentação de referência, guia de utilização e notas técnicas. |
| `LogoDroneGov.png` / `.svg` / `.webp` | Arquivos de imagem do logotipo padrão DroneGov em diversos formatos. |
| [LICENSE](file:///d:/Visualizador360_DroneGov/LICENSE) | Licença de código aberto (MIT License). |

---

## ⚠️ Solução de Problemas (Troubleshooting)

* **Botão "Alterar Logo" Não Responde:** Certifique-se de que está utilizando a versão atualizada do `360_Gerador.html`, onde a inicialização da marca foi corrigida.
* **Imagem Distorcida:** Verifique se a foto original foi capturada no modo panorâmico esférico equirretangular (proporção de aspecto 2:1).
* **Arquivo Muito Grande para o SEI:** O limite prático de upload no SEI costuma ser entre 5 MB e 10 MB. Se a imagem for muito pesada, recomenda-se redimensioná-la para 4096×2048px ou comprimi-la ligeiramente antes do processamento.
* **Tela Preta no Visualizador:** Pode ocorrer caso os scripts CDN não consigam carregar por bloqueio de rede corporativa. O arquivo gerado utiliza carregamento resiliente.

---

## 📜 Licença

Este projeto é distribuído sob a **Licença MIT**. Consulte o arquivo [LICENSE](file:///d:/Visualizador360_DroneGov/LICENSE) para mais detalhes.

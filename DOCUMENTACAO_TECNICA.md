# 📘 Documentação Técnica de Referência — Visualizador 360° DroneGov

**Versão da Documentação:** 1.1 (Shader Esférico Per-Pixel, Correção do Nadir e funcionamento 100% offline)  
**Data:** 2026  
**Projeto:** Visualizador 360° DroneGov  
**Repositório:** [https://github.com/KuriakinToscan/Visualizador360_DroneGov](https://github.com/KuriakinToscan/Visualizador360_DroneGov)

---

## 📄 1. Visão Geral e Arquitetura do Sistema

O **Visualizador 360° DroneGov** é uma solução web desenvolvida para possibilitar a exibição de fotografias esféricas panorâmicas (capturadas por drones ou câmeras 360°) em ambientes administrativos restritos, especificamente o **Sistema Eletrônico de Informações (SEI)** do Governo Federal.

### 🏛️ Arquitetura Zero-Dependência (Offline First)
* **Visualizador Autônomo:** O arquivo HTML gerado é totalmente autocontido. A imagem panorâmica e os logotipos (em Base64), os estilos CSS, os metadados selecionados e o motor de renderização 3D (Three.js r128) são embutidos no próprio arquivo. Nenhum recurso é carregado da internet.
* **Gerador Autônomo:** O `360_Gerador.html` também funciona sem internet: a biblioteca EXIF-js, os ícones (Phosphor, em SVG) e o logotipo DroneGov estão embutidos, e as fontes são as do sistema operacional.
* **Segurança e Privacidade:** Nenhum dado da imagem ou metadado é enviado para servidores externos. Todo o processamento de conversão e leitura de EXIF ocorre localmente no navegador do usuário.

---

## 🛠️ 2. Estrutura do Gerador (`360_Gerador.html`)

O arquivo [`360_Gerador.html`](360_Gerador.html) atua como uma aplicação web única que combina a interface de geração, manipulação de marca, leitura de EXIF e montagem do código do visualizador.

```
┌────────────────────────────────────────────────────────┐
│                   360_Gerador.html                     │
├────────────────────────────────────────────────────────┤
│  0. Recursos embutidos: EXIF-js, ícones SVG,           │
│     logotipo WebP e código do Three.js (text/plain)    │
│  1. Módulo de Marca & Títulos (localStorage)           │
│  2. Módulo de Upload & Leitura de EXIF (exif-js)       │
│  3. Módulo de Compilação & Embutimento Base64          │
│  4. Módulo do Modal de Ajuda Interativo                │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
            ┌─────────────────────────────┐
            │   Arquivo HTML Autônomo     │
            │   (Visualizador 360°)       │
            └─────────────────────────────┘
```

---

## 🎨 3. Módulo de Marca Institucional & Persistência

### 💾 Armazenamento no `localStorage`
O gerador salva automaticamente as preferências do usuário no navegador para que sejam reutilizadas em gerações futuras:

| Chave `localStorage` | Descrição | Valor Padrão Oficial |
| :--- | :--- | :--- |
| `dronegov_custom_logo` | String Base64 / Data URL da logo | Logotipo DroneGov em WebP (`DEFAULT_LOGO`, que aponta para `FIXED_DRONEGOV_LOGO_DATA_URL`) |
| `dronegov_custom_title` | Texto do Título Principal | `"DRONEGOV"` |
| `dronegov_custom_subtitle` | Texto do Subtítulo / Órgão | `"Associação Brasileira de Aviação Pública Não Tripulada e Sistemas Embarcados"` |

### 🔄 Fluxo de Manipulação da Marca
1. **Upload:** O botão `btn-upload-logo` dispara o input de arquivo oculto `logo-file-input`.
2. **Validação:** Imagens acima de 4MB são rejeitadas para evitar estouro de memória no `localStorage`.
3. **Conversão:** O arquivo é lido por um `FileReader` e convertido para uma Data URL (`data:image/...;base64,...`).
4. **Restauração:** O botão `btn-reset-brand` limpa as chaves no `localStorage` e restaura as marcas e títulos originais oficiais do DroneGov.

> [!TIP]
> **Atualização de Versões e Cache:** Quando novas versões do gerador forem publicadas com logotipos ou títulos padrão atualizados, navegadores de usuários que já utilizaram o gerador anteriormente priorizarão as preferências salvas no `localStorage`. Para visualizar os novos padrões oficiais, basta clicar em **"Restaurar Padrão"** na interface ou executar `localStorage.clear()` no console.

---

## 📸 4. Módulo de Extração e Seleção Seletiva de Metadados EXIF

A extração de metadados utiliza a biblioteca `exif-js` integrada no cabeçalho do documento.

### 📊 Tags EXIF Extraídas

```javascript
var meta = {
  model: EXIF.getTag(this, 'Model') || 'Não identificado',
  dateTime: EXIF.getTag(this, 'DateTimeOriginal') || EXIF.getTag(this, 'DateTime') || 'Não informada',
  lat: EXIF.getTag(this, 'GPSLatitude'),
  latRef: EXIF.getTag(this, 'GPSLatitudeRef'),
  lon: EXIF.getTag(this, 'GPSLongitude'),
  lonRef: EXIF.getTag(this, 'GPSLongitudeRef')
};
```

### 🎛️ Filtro e Controle Seletivo de Exibição
O gerador disponibiliza controles (*checkboxes*) para que o operador decida quais dados integrar ao cabeçalho do visualizador:
* `check-meta-model`: ativa/desativa a inclusão do Modelo do Drone;
* `check-meta-datetime`: ativa/desativa a inclusão da Data e Hora da Captura;
* `check-meta-gps`: ativa/desativa a inclusão das Coordenadas Geográficas.

Se todos forem desmarcados e nenhuma descrição for informada, o contêiner `.sub-info` é automaticamente omitido, gerando um cabeçalho compacto e focado exclusivamente na identidade visual.

### 🌐 Conversão de Coordenadas GPS
As coordenadas GPS lidas do EXIF em Graus, Minutos e Segundos são convertidas para graus decimais, exibidos com 6 casas (ex.: `-25.428400, -49.273300`):
$$\text{DD} = \text{Graus} + \frac{\text{Minutos}}{60} + \frac{\text{Segundos}}{3600}$$
com sinal negativo para os hemisférios Sul (`S`) e Oeste (`W`).

### 🕒 Data e Hora
`DateTimeOriginal` (ou `DateTime`) é exibido no formato brasileiro, com segundos (ex.: `12/05/2024, 14:33:10`).

---

## 🌐 5. Motor de Renderização 3D v1.1 (Three.js + Shader GLSL)

O visualizador final gerado utiliza a biblioteca **Three.js (r128)**, embutida no próprio arquivo, combinada com um Shader customizado per-pixel em GLSL (`THREE.ShaderMaterial`) rodando em `<canvas>` WebGL.

### 📐 Parâmetros da Geometria e Material Esférico

| Parâmetro | Valor | Descrição |
| :--- | :--- | :--- |
| **Geometria** | `THREE.SphereGeometry(500, 48, 48)` | Esfera de raio 500 com amostragem otimizada para o shader per-pixel. |
| **Material / Shader** | `THREE.ShaderMaterial` (GLSL per-pixel) | Projeção trigonométrica equirretangular em fragment shader, eliminando o "beliscão polar" e distorções no Nadir (solo abaixo do drone). |
| **Lado da Face** | `THREE.BackSide` | Renderização nativa da face interna da esfera. |
| **Câmera** | `THREE.PerspectiveCamera` + Euler `'YXZ'` | Ordem de rotação Euler 'YXZ' para evitar *gimbal lock*; inclinação vertical permitida de -89.9° a +89.9°. |
| **Filtragem de Textura** | `LinearFilter` (Sem Mipmaps) | `t.generateMipmaps = false`, garantindo filtragem linear pura e eliminando emendas no solo. |
| **Assinatura de Saída** | UTF-8 BOM (`\uFEFF`) | Injetado no Blob para perfeita acentuação em ambientes Windows e navegadores internos do SEI. |

### 🎮 Interação e Controles

* **Interpolação de Arraste (Lerp):** Evita movimentos bruscos durante a navegação com o mouse (não há suporte a gestos de toque nesta versão).
* **Zoom:** Roda do mouse ou botões `+`/`−`, com campo de visão (FOV) entre 30° e 110°.
* **Auto-Rotate:** Alterna a rotação automática da esfera a uma velocidade constante de `0.5°` por frame.
* **Redefinição de Vista:** Botão de *Reset* que retorna o FOV para 75° e o ângulo inicial da câmera para $(0, 0)$.

---

## 🧩 6. Montagem do Arquivo Gerado e Tamanho

O modelo do visualizador (`VIEWER_TPL`) contém marcadores que o gerador substitui no momento da geração:

| Marcador | Conteúdo |
| :--- | :--- |
| `__IMG__` | Imagem panorâmica (Data URL Base64), presente **uma única vez**; a miniatura da tela inicial reaproveita a mesma imagem via JavaScript |
| `__LOGO__` | Logotipo ativo (padrão DroneGov em WebP ou o logotipo personalizado) |
| `__FIXED_DRONEGOV_LOGO__` | Logotipo do selo "Powered by DroneGov" |
| `__TITLE__`, `__HEADER_TITLE__`, `__HEADER_SUBTITLE__` | Nome do arquivo, título e subtítulo (com escape de HTML) |
| `__SUB_INFO_SECTION__` | Badges dos metadados selecionados e da descrição |
| `__THREE_JS__` | Código do Three.js r128, lido do bloco `<script type="text/plain" id="three-lib-src">` do gerador |

As substituições usam funções (`replace(regex, function)`), para que caracteres como `$` presentes no código do Three.js não sejam interpretados como padrões de substituição.

**Tamanho do visualizador ≈ 1,33 × tamanho da foto + 0,9 MB** (acréscimo da codificação Base64 mais o motor 3D e os logotipos). Exemplo medido: foto 4096 × 2048 de 3,8 MB → visualizador de 5,8 MB.

---

## 📑 7. Especificações de Integração com o SEI

Para garantir o funcionamento adequado dentro do ambiente do SEI (Sistema Eletrônico de Informações):

1. **Formato do Documento:** O arquivo deve ser cadastrado obrigatoriamente como documento **Externo**, formato **Nato-digital**.
2. **Limite de Tamanho:** O limite de upload costuma ficar entre **5 MB e 10 MB**, conforme a instalação do SEI. Use a fórmula da seção 6 para estimar o tamanho; se necessário, reduza a foto para 4096 × 2048 px.
3. **Compatibilidade de Navegadores:** Navegadores com WebGL: Google Chrome, Microsoft Edge, Mozilla Firefox e Brave.
4. **Funcionamento sem rede:** Como todos os recursos estão embutidos, o visualizador funciona mesmo em redes corporativas que bloqueiam CDNs e acesso externo.

---

## 📜 8. Licenciamento e Créditos

* **Licença:** MIT License
* **Autor Original:** Gilberto Milhomem Marinho Filho
* **Evolução de Interface, Motor de Renderização e Versão 1.1:** Kuriakin Toscan (2026)
* **Metadados EXIF, Personalização de Marca e Descrição:** Luiz Augusto / laoc81 (2026)
* **Bibliotecas embutidas (MIT):** Three.js r128 (© 2010-2021 Three.js Authors), EXIF-js 2.3.0 (© Jacob Seidelin), Phosphor Icons (© Phosphor Icons)

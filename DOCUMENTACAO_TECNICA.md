# 📘 Documentação Técnica de Referência — Visualizador 360° DroneGov

**Versão da Documentação:** 1.1 (Shader Esférico Per-Pixel & Correção Nadir)  
**Data:** 2026  
**Projeto:** Visualizador 360° DroneGov  
**Repositório:** [https://github.com/KuriakinToscan/Visualizador360_DroneGov](https://github.com/KuriakinToscan/Visualizador360_DroneGov)

---

## 📄 1. Visão Geral e Arquitetura do Sistema

O **Visualizador 360° DroneGov** é uma solução web desenvolvida para possibilitar a exibição de fotografias esféricas panorâmicas (capturadas por drones ou câmeras 360°) em ambientes administrativos restritos, especificamente o **Sistema Eletrônico de Informações (SEI)** do Governo Federal.

### 🏛️ Arquitetura Zero-Dependência (Offline First)
* **Visualizador Autônomo:** O arquivo HTML gerado é totalmente autocontido. Todas as mídias (imagem panorâmica, logotipos institucionais), estilos CSS, metadados EXIF e o motor de renderização 3D são embutidos em Base64/Inline.
* **Segurança e Privacidade:** Nenhum dado da imagem ou metadado é enviado para servidores externos. Todo o processamento de conversão e leitura de EXIF ocorre localmente no navegador do usuário.

---

## 🛠️ 2. Estrutura do Gerador (`360_Gerador.html`)

O arquivo [360_Gerador.html](file:///d:/Visualizador360_DroneGov/360_Gerador.html) atua como uma aplicação web única que combina a interface de geração, manipulação de marca, leitura de EXIF e montagem do código do visualizador.

```
┌────────────────────────────────────────────────────────┐
│                   360_Gerador.html                     │
├────────────────────────────────────────────────────────┤
│  1. Módulo de Marca & Títulos (localStorage)           │
│  2. Módulo de Upload & Leitura de EXIF (exif-js)       │
│  3. Módulo de Compilação & Embutimento Base64           │
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
| `dronegov_custom_logo` | String Base64 / Data URL da logo | Logotipo SVG DroneGov (`LOGO_DRONEGOV_SVG_BASE64`) |
| `dronegov_custom_title` | Texto do Título Principal | `"DRONEGOV"` |
| `dronegov_custom_subtitle` | Texto do Subtítulo / Órgão | `"Associação Brasileira de Aviação Pública Não Tripulada e Sistemas Embarcados"` |

### 🔄 Fluxo de Manipulação da Marca
1. **Upload:** O botão `btn-upload-logo` dispara o input de arquivo oculto `logo-file-input`.
2. **Validação:** Imagens acima de 4MB são rejeitadas para evitar estouro de memória no `localStorage`.
3. **Conversão:** O arquivo é lido por um `FileReader` e convertido para uma Data URL (`data:image/...;base64,...`).
4. **Restauração:** O botão `btn-reset-brand` limpa as chaves no `localStorage` e restaura as marcas e títulos originais oficiais do DroneGov.

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
As coordenadas GPS lidas do EXIF em formato racional de Graus, Minutos e Segundos são convertidas para o formato decimal e sexagesimal padrão:
$$\text{Coordenada} = DD^\circ\,MM'\,SS.S''\,\text{Direction}$$

---

## 🌐 5. Motor de Renderização 3D v1.1 (Three.js + Shader GLSL)

O visualizador final gerado utiliza a biblioteca **Three.js (r128)** combinada com um Shader customizado per-pixel em GLSL (`THREE.ShaderMaterial`) rodando em `<canvas>` WebGL.

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

* **Interpolação de Arraste (Lerp):** Evita movimentos bruscos durante a navegação com o mouse ou tela sensível ao toque.
* **Auto-Rotate:** Alterna a rotação automática da esfera a uma velocidade constante de `0.1°` por frame.
* **Redefinição de Vista:** Botão de *Reset* que retorna o FOV para 75° e o ângulo inicial da câmera para $(0, 0)$.

---

## 📑 6. Especificações de Integração com o SEI

Para garantir o funcionamento adequado dentro do ambiente do SEI (Sistema Eletrônico de Informações):

1. **Formato do Documento:** O arquivo deve ser cadastrado obrigatoriamente como documento **Externo**, formato **Nato-digital**.
2. **Limite de Tamanho:** Recomenda-se que o arquivo HTML gerado tenha tamanho final entre **5 MB e 10 MB** (limite aceito na maioria das instalações do SEI).
3. **Compatibilidade de Navegadores:** Testado e suportado nativamente no Google Chrome, Microsoft Edge, Mozilla Firefox e Brave.

---

## 📜 7. Licenciamento e Créditos

* **Licença:** MIT License
* **Autor Original:** Gilberto Milhomem Marinho Filho
* **Evolução de Interface & Funcionalidades:** Kuriakin Toscan (2026) & Luiz Augusto / laoc81 (2026)
* **Instituição:** IBAMA — Instituto Brasileiro do Meio Ambiente e dos Recursos Naturais Renováveis (DIPAM-TO)

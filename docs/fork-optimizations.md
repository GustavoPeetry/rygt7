# RyGT7 com otimizações experimentais

Fork comunitário para Windows x64, mantido por Gustavo Petry. A base é a release oficial [gt7-2026-11-16](https://github.com/RyGT-Team/rygt7/releases/tag/gt7-2026-11-16), no [commit `7dbe4e21b3d26027014efaed694d1eb07c265a0a`](https://github.com/RyGT-Team/rygt7/commit/7dbe4e21b3d26027014efaed694d1eb07c265a0a). Ela traz o launcher 0.1.7. O launcher deste fork é o **0.1.8**. O teste local usou o renderer oficial separado [shadGT7 `3e2b8d76`](https://github.com/RyGT-Team/shadgt7/commit/3e2b8d76d2c38b5da09aadf5b8be7ff76d7f6d64).

## Mudanças em relação à versão oficial

| Área | Mudança |
| --- | --- |
| Áudio | Substitui a alimentação de fila por polling por um mixer SDL acionado por callback, com buffers circulares por porta e uma saída estéreo compartilhada de 48 kHz. Inclui diagnóstico opcional e ajuste de prioridade de threads de áudio no Windows. |
| Leitura de arquivos | Usa offsets explícitos em `ReadFile` e `WriteFile` no caminho AIO do Windows. Aumenta o buffer de leitura reutilizável para 4 MiB por thread e passa a tentar de novo a leitura direta antes de recorrer ao buffer auxiliar. |
| Caminhos e inicialização | Memoriza até 32.768 resoluções de caminho sob `/app0` e permite leituras simultâneas das tabelas de diretório e aliases. Ao abrir `GT7.exe` diretamente no Windows, o runtime também lê as linhas `env=1|NOME=valor` de `RyGT7.ini`, respeitando valores de ambiente já definidos. |
| Shaders e launcher | A opção **Settings → Shaders → Precompile shaders before starting game** inicia o pré-aquecedor Vulkan instalado em `tools\ryty_prewarm.exe`. A ferramenta audita o armazenamento SPIR-V, lê arquivos `.meta` para aquecer o cache do sistema e compila receitas compute compatíveis registradas em sessões anteriores. O launcher exibe progresso e oferece cancelamento. |

Os pipelines gráficos de conteúdo ainda inédito continuam sendo compilados no jogo. A utilidade do pré-aquecimento depende do cache já formado, da GPU e do driver. FPS, estabilidade e qualidade de áudio em corrida ainda precisam de validação comparativa.

## Compilar e usar

1. Inicialize os submódulos com `git submodule update --init --recursive` e siga o [guia de compilação](../build-support/README.md) para runtime, launcher e renderer.
2. No mesmo build CMake do runtime, execute `cmake --build build --target ryty_prewarm`. Copie `build/tools/ryty_prewarm/ryty_prewarm.exe` para `tools\ryty_prewarm.exe` no diretório de instalação do runtime. O empacotador herdado não inclui a ferramenta automaticamente.
3. Ative a opção na aba **Shaders**. Ela vem desligada. Se o executável auxiliar estiver ausente, o launcher inicia o jogo diretamente. Para inspecionar o cache sem iniciar o jogo, execute `ryty_prewarm.exe inspect --root "<pasta de instalação>" --verify-store`.

Os links de download e o atualizador do launcher apontam para as versões oficiais do RyGT7. Uma atualização oficial pode substituir componentes compilados deste fork.

## Validação

No Windows, a revisão confirmou 82 verificações de setup do launcher, os testes de logging e de overlay de `/app0`, e a compilação independente do pré-aquecedor. Na RTX 2060 SUPER, a auditoria identificou 27.826 arquivos SPIR-V, zero arquivos sinalizados como inválidos pelo auditor e 179 receitas compute. A revisão não executou uma corrida comparativa nem gerou um ZIP distribuível do fork.

O código e os avisos de licença seguem o projeto de origem. [Licença](../LICENSE) · [Créditos](../AUTHORS.md) · [Histórico do launcher](../loader/CHANGELOG.md) · [Repositório oficial](https://github.com/RyGT-Team/rygt7).

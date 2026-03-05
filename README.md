[placeholder: title-screen] (Assets/Media/Wallpaper.png)
DungeonSweeper — Guia do Desenvolvedor

Este repositório contém um template de jogo inspirado em Vampire Survivor, focado em mecânicas de sobrevivência, *waves* de inimigos e upgrades rápidos. O projeto usa prefabs e Unity Visual Scripting (Graph Machines) como núcleo das mecânicas; este README foi escrito para ajudar desenvolvedores a entender, estender e criar novas mecânicas além das mostradas no template.

**Resumo rápido**:
- **Objetivo**: fornecer uma base para aprender a criar mecânicas de jogo tipo Vampire Survivor e expandi-las.
- **Tecnologias**: Unity (Visual Scripting / Bolt), prefabs, alguns utilitários C# (plugin PlayerPrefsEditor incluído).

**Como usar este README**: siga as seções abaixo para localizar sistemas principais, entender os ScriptGraphs existentes, ver pontos de extensão e ver exemplos de integração em C# quando quiser misturar código com Visual Scripting.

**Nota**: Este README foca em desenvolvimento — se precisar de um README para jogadores, me peça uma versão mais curta.

[placeholder: gameplay-screen] (Assets/Media/Gameplay.png)

## Project structure

Principais pastas e pontos de interesse

- **Projetos e assets do jogo**: [Assets/_Game](Assets/_Game)
  - ScriptGraphs (fluxos de jogo): [Assets/_Game/ScriptGraphs](Assets/_Game/ScriptGraphs)
  - Conteúdo (prefabs de personagens, inimigos e ataques): [Assets/_Game/Content](Assets/_Game/Content)
  - Cenas: [Assets/_Game/Scenes](Assets/_Game/Scenes)
- **Pacotes e configurações do projeto**: [ProjectSettings](ProjectSettings)
- **Plugin utilitário incluído**: PlayerPrefsEditor (editor e amostra): [Assets/PlayerPrefsEditor](Assets/PlayerPrefsEditor)

Abra o projeto no Unity (versão compatível com URP e Visual Scripting). As *Graph Machines* estão associadas a prefabs e ativos dentro de `Assets/_Game`.

**ScriptGraphs principais (visão geral)**

Os principais Graph assets estão em [Assets/_Game/ScriptGraphs](Assets/_Game/ScriptGraphs). Eles implementam a maior parte da lógica do template usando Unity Visual Scripting. Arquivos notáveis:

- [Assets/_Game/ScriptGraphs/Character.asset](Assets/_Game/ScriptGraphs/Character.asset) : lógica de personagem (movimento, hit/health, triggers de ataque e recebimento de dano).
- [Assets/_Game/ScriptGraphs/Projectile.asset](Assets/_Game/ScriptGraphs/Projectile.asset) : comportamento de projéteis (movimento, colisões, efeitos ao impacto).
- [Assets/_Game/ScriptGraphs/SkillSpawner.asset](Assets/_Game/ScriptGraphs/SkillSpawner.asset) : sistema que gerencia spawn e execução de skills/ataques ligados ao jogador.
- [Assets/_Game/ScriptGraphs/GameplayStates.asset](Assets/_Game/ScriptGraphs/GameplayStates.asset) : gerenciamento de estados do jogo (menu, jogo ativo, pausa, game over).
- [Assets/_Game/ScriptGraphs/GameplayHUD.asset](Assets/_Game/ScriptGraphs/GameplayHUD.asset) : atualiza HUD (vida, mana, contadores de inimigos, etc.).
- [Assets/_Game/ScriptGraphs/MenuCanvasController.asset](Assets/_Game/ScriptGraphs/MenuCanvasController.asset) : controle de menus e navegação UI.

Esses Graphs são o ponto de partida para entender como as entidades interagem — abrir os Graphs no editor Visual Scripting mostra os eventos, variáveis e fluxos usados.

**Conteúdo (prefabs)**

Arquivos de conteúdo estão em [Assets/_Game/Content](Assets/_Game/Content). Itens úteis para copiar/modificar:

- [Assets/_Game/Content/Characters/Player.prefab](Assets/_Game/Content/Characters/Player.prefab) — prefab base do jogador.
- [Assets/_Game/Content/Characters/Enemy.prefab](Assets/_Game/Content/Characters/Enemy.prefab) — prefab base de inimigo.
- [Assets/_Game/Content/Attacks/Projectile.prefab](Assets/_Game/Content/Attacks/Projectile.prefab) — projétil usado como exemplo. Crie cópias para novas armas/ataques.

Para criar uma nova arma/skill: duplique o prefab de ataque, atualize o componente `Graph Machine` para apontar a nova Graph (ou altere variáveis expostas) e adicione o prefab numa lista gerenciada pelo `SkillSpawner` (ver Graph).

**Scripts C# customizados**

- PlayerPrefsEditor plugin (editor utilities): arquivos em [Assets/PlayerPrefsEditor](Assets/PlayerPrefsEditor).
- Exemplo de controlador de PlayerPrefs usado pela amostra: [Assets/PlayerPrefsEditor/Samples/SampleScene/PlayerPrefsController.cs](Assets/PlayerPrefsEditor/Samples/SampleScene/PlayerPrefsController.cs)

Observação: a maior parte da lógica do jogo está implementada via Visual Scripting; não há muitos scripts de jogo customizados em C# neste template além do plugin utilitário. Se quiser implementar lógica performática ou reutilizável em C#, veja a seção "Integração C#" abaixo.

**Como estender e criar novas mecânicas**

Siga estes passos e recomendações para desenvolver novas mecânicas além das fornecidas no template.

1. Entenda os Graphs existentes
   - Abra [Assets/_Game/ScriptGraphs/SkillSpawner.asset](Assets/_Game/ScriptGraphs/SkillSpawner.asset) e [Assets/_Game/ScriptGraphs/Character.asset](Assets/_Game/ScriptGraphs/Character.asset).
   - Identifique eventos de entrada (por exemplo, entrada do jogador, timers, eventos de colisão). Procure por variáveis expostas (Graph variables) que definem velocidade, dano, taxa de fogo.

2. Criar um novo ataque/skill
   - Duplique um prefab em [Assets/_Game/Content/Attacks](Assets/_Game/Content/Attacks) (ex.: `Projectile.prefab`).
   - Copie o Graph associado a `Projectile.asset` ou crie um novo Graph para o comportamento desejado (p.ex. ricochete, AOE, orbit).
   - Exponha parâmetros (dano, velocidade, lifespan) como variáveis do Graph para que o `SkillSpawner` ou o prefab que instancia o ataque possa configurá-los.

3. Adicionar novos inimigos
   - Duplique [Assets/_Game/Content/Characters/Enemy.prefab](Assets/_Game/Content/Characters/Enemy.prefab) e altere sprites/modelos, stats e adicione um Graph de AI (ou reutilize um Graph existente com parâmetros diferentes).
   - Para comportamentos complexos, escreva um Graph com estados (patrulha, perseguição, atacar) e use `Custom Events` para comunicar hits e mortes ao `GameplayStates`/HUD.

4. Expandir o sistema de upgrades
   - Crie um sistema de dados (pode ser um `ScriptableObject`) para definir upgrades: custo, efeito, escalonamento.
   - Quando um upgrade for adquirido, envie um evento para o `Character` Graph para alterar parâmetros (ex.: aumentar taxa de fogo). Visual Scripting pode receber `Custom Event` que modifica variáveis do Graph.

5. Mix de Visual Scripting e C# (quando usar C#)
   - Use C# para código crítico em performance (física de projéteis massivos, pathfinding personalizado) ou para lógica que você queira compartilhar fora do Unity Editor.
   - Exponha hooks (UnityEvents ou métodos públicos) que os Graphs podem chamar via `Call Method`/`Invoke` ou use `SendMessage`/`Broadcast` com cuidado.

6. Testes iterativos
   - Trabalhe com instâncias pequenas: crie uma cena de teste com apenas jogador + 1 inimigo + 1 skill.
   - Use `Play Mode` e altere variáveis expostas no prefab para procurar o tweak ideal.

**Exemplos práticos e dicas**

- Criar um projétil que perfura inimigos: crie um `Projectile` novo e, no Graph, em vez de destruir ao colidir, decrementa um contador `pierceCount` e só destrói quando atingir 0.

- Limitar número de projéteis ativos para performance: gerencie um pool (pode ser um pequeno C# `ObjectPool` ou um Graph que reuse instâncias).

- Adicionar habilidades passivas (AoE automático, life steal): crie um Graph ligado ao `Character` que escuta eventos de ataque e aplica efeitos adicionais.

**Integração C# — snippet de exemplo**

Quando quiser adicionar código C# que envia eventos para Visual Scripting, um padrão simples é ter um `MonoBehaviour` que dispara `UnityEvent`s na colisão. Exemplo mínimo:

```csharp
using UnityEngine;
using UnityEngine.Events;

public class ProjectileCollision : MonoBehaviour
{
    public UnityEvent<GameObject> onHit; // pode ser ligado no editor a um Graph via Call
    public float damage = 10f;

    void OnTriggerEnter2D(Collider2D other)
    {
        if (other.CompareTag("Enemy"))
        {
            onHit?.Invoke(other.gameObject);
        }
    }
}
```

Insira este componente em `Projectile.prefab` e, no Graph do projétil ou do inimigo, use o evento para reduzir vida, spawnar partículas ou gerar efeitos.

**Scripts utilitários incluídos**

- O plugin `PlayerPrefsEditor` está disponível em [Assets/PlayerPrefsEditor](Assets/PlayerPrefsEditor). Ele traz ferramentas de editor e um exemplo (`PlayerPrefsController.cs`) para manipular preferências de jogador na cena de amostra.

[**Placeholders para imagens**]

[placeholder: Gameplay screenshot] (Assets/Media/Gameplay.png)

[placeholder: Wallpaper / artwork] (Assets/Media/Wallpaper.png)

Substitua os arquivos acima em `Assets/Media/` (crie a pasta) e atualize os nomes se necessário.

**Próximos passos sugeridos**

- Experimente criar um novo ataque seguindo o fluxo: duplicar prefab de `Projectile` → ajustar Graph → expor variáveis → adicionar ao `SkillSpawner`.
- Se preferir escrever lógica em C#, crie um pequeno `ObjectPool` para projéteis e exponha métodos públicos para o `SkillSpawner` chamar via `Call Method`.
- Posso gerar exemplos prontos: C# `ObjectPool`, um `ScriptableObject` para upgrades, ou Graph templates para IA — diga qual prefere.

Se quiser, gero automaticamente um exemplo de `ObjectPool` ou um `ScriptableObject` para gerenciar upgrades.

---

Se desejar, adapto este README para incluir screenshots reais, fluxos de uso passo a passo mais detalhados, ou adiciono exemplos práticos (C# + Graph) diretamente no projeto.

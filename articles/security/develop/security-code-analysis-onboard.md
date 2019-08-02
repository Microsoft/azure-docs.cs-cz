---
title: Průvodce registrací analýzy kódu Microsoft Azure zabezpečení
description: Tento článek se týká instalace rozšíření analýzy kódu zabezpečení
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718298"
---
# <a name="how-to-onboarding-and-installing"></a>Jak: Připojování a instalace

Požadavky, které vám pomohou začít s analýzou kódu zabezpečení Microsoftu
  - Opravňující nabídka služby Microsoft Unified Support Services (podrobnosti najdete níže)
  - Organizace Azure DevOps
  - Oprávnění k instalaci rozšíření do organizace Azure DevOps
  - Zdrojový kód, který se dá synchronizovat s kanálem Azure DevOps hostovaným v cloudu


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>Připojování rozšíření pro analýzu kódu Microsoft Security

- Pokud už máte jednu z následujících nabídek podpory, stačí se obrátit na svého technického správce účtů a purchase\swap stávající hodiny, abyste získali přístup k tomuto rozšíření.
   - Sjednocená podpora – Pokročilá úroveň výkonu, Premier Support pro vývojáře, Premier Support pro partnery nebo Premier Support pro podniky.
- Pokud máte jednu z následujících služeb podpory nebo nemáte žádný plán podpory pro Microsoft, budete muset upgradovat na opravňující nabídku podpory:
   - Podpora Azure pro partnery, Azure Basic, Azure Developer, Azure Standard, Azure Professional Direct nebo Unified support – úroveň Core
- Pokud si chcete koupit nabídku opravňující podpory, navštivte prosím naši [domovskou stránku služeb podpory](https://www.microsoft.com/enterprise/services/support) .
- Po dokončení smlouvy o podpoře se obraťte na svého technického správce účtu, který vám může pomoci začít a shromáždit všechny požadované podrobnosti.
 
>[!NOTE]
> Pouze registrovaní partneři v síti Microsoft Partner mají nárok na zakoupení Premier Support pro partnery, jinak si můžete koupit jednu z oprávněných nabídek podpory, které jsme uvedli dřív.

## <a name="installing-microsoft-security-code-analysis-extension"></a>Instalace rozšíření Microsoft Security Code Analysis Extension

1. Jakmile se rozšíření sdílí s vaší organizací Azure DevOps, přejděte na stránku organizace Azure DevOps (například http://dev.azure.com/contoso)
2. Klikněte na ikonu nákupního balíčku v pravém horním rohu vedle svého jména a pak klikněte na spravovat rozšíření. 
3. Klikněte na rozšíření Microsoft Security Code Analysis Extension a spusťte instalaci spuštěním Průvodce uživatelským rozhraním Azure DevOps.
4. Vyberte organizaci Azure DevOps, do které chcete rozšíření nainstalovat z rozevíracího seznamu.
5. Klikněte na nainstalovat. Až se dokončí, můžete pokračovat v používání rozšíření.

>[!NOTE]
> I v případě, že nemáte přístup, pokračujte v krocích instalace a budete moct během procesu požádat o přístup od správce vaší organizace Azure DevOps.
>
Po nainstalování rozšíření budou úkoly pro bezpečné vytváření sestavení viditelné a k dispozici pro přidání do Azure Pipelines.

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>Přidání konkrétních úloh sestavení do kanálu DevOps

1. Otevřete týmový projekt z vaší organizace Azure DevOps.
2. Přejděte na kartu **buildy** v části **kanály** . 
3. Vyberte kanál, do kterého chcete přidat úlohy sestavení rozšíření. 
   - Klikněte na **Nový** a postupujte podle pokynů pro vytvoření nového kanálu.
   - Upravit – vyberte kanál a kliknutím na **Upravit** začněte upravovat existující kanál.
4. Kliknutím na + přejděte do podokna **Přidat úlohy** .
5. Vyhledejte úkol sestavení, který chcete přidat buď ze seznamu, nebo pomocí vyhledávacího pole a pak klikněte na tlačítko **Přidat**. 
6. Nyní můžete pokračovat zadáním parametrů potřebných pro úlohu.
>[!NOTE]
>Cesty k souboru nebo adresáři jsou relativní ke kořenu zdrojového úložiště a parametry, které určují, že výstupní složka/soubory budou nahrazeny běžným umístěním, které jsme definovali v agentovi sestavení.

7. Zařadit nové sestavení do fronty
> [!TIP]
>  - Chcete-li po sestavení spustit analýzu, umístěte úkoly sestavení analýzy kódu zabezpečení společnosti Microsoft po kroku sestavení artefaktů sestavení. Tímto způsobem vaše sestavení může dokončit a vystavit výsledky před spuštěním nástrojů statické analýzy.
>  - Vždy v možnosti **pokračovat při chybě v** úlohách sestavení pro zabezpečení vývoje vždy kontrolujte. I v případě, že se některý z nástrojů nezdařil, mohou uživatelé spustit jiné. Neexistují žádné vzájemné závislosti.
>  - Úkoly sestavení analýzy kódu zabezpečení společnosti Microsoft selžou pouze v případě, že nástroj selže v úspěšném spuštění, ale v případě, kdy nástroj identifikuje problémy v kódu, se nezdaří. Můžete nakonfigurovat, aby se vaše sestavení přerušit, když nástroj identifikuje problémy v kódu pomocí úlohy sestavení **po analýze** .
>  - Některé úlohy sestavení Azure DevOps nejsou podporované, když je spustíte prostřednictvím kanálu Release. Toto je omezení Azure DevOps. Nepodporují úlohy, které publikují artefakty v rámci kanálu vydání.
>  - Seznam předdefinovaných proměnných ve službě Azure DevOps Team Build, které můžete zadat jako parametry, najdete v tématu [proměnné sestavení Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts) .

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci úloh sestavení najdete v našem [Průvodci konfigurací](security-code-analysis-customize.md) .

Pokud máte další dotazy týkající se rozšíření a nabízených nástrojů, podívejte se na [stránku Nejčastější dotazy.](security-code-analysis-faq.md)



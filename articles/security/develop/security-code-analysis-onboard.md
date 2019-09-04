---
title: Průvodce registrací Microsoft Security Code Analysis
description: Tento článek popisuje instalaci rozšíření Microsoft Security Code Analysis Extension.
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
ms.openlocfilehash: 8936bc21af951cbabb90188b6a31d610caf80395
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241774"
---
# <a name="onboarding-and-installing"></a>Připojování a instalace

Požadavky na Začínáme s analýzou kódu zabezpečení Microsoftu:

- Opravňující nabídka Microsoft Unified Support, jak je popsáno v následující části.
- Organizace Azure DevOps.
- Oprávnění k instalaci rozšíření do organizace Azure DevOps.
- Zdrojový kód, který lze synchronizovat s kanálem Azure DevOps hostovaným v cloudu.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Připojování rozšíření Microsoft Security Code Analysis Extension

- Pokud máte jednu z následujících nabídek podpory, požádejte svého technického manažera o zakoupení nebo prohození stávajících hodin za účelem získání přístupu k rozšíření:
   - Rozšířená vrstva sjednocené podpory
   - Úroveň výkonu sjednocené podpory
   - Premier Support pro vývojáře
   - Premier Support pro partnery
   - Premier Support pro podniky
- Pokud máte jednu z následujících služeb podpory nebo nemáte žádný plán podpory od Microsoftu, musíte upgradovat na opravňující nabídku podpory:
   - Podpora Azure pro partnery
   - Podpora Azure na úrovni Basic
   - Developer Support Azure
   - Standard Support Azure
   - Professional Direct Azure
   - Základní úroveň sjednocené podpory
- Pokud si chcete koupit opravňující nabídku podpory, navštivte [domovskou stránku naší služby podpory](https://www.microsoft.com/enterprise/services/support).
- Po dokončení smlouvy o podpoře se obraťte na svého zástupce Technical Account Manager, který vám usnadní práci a vám usnadní shromažďování všech potřebných údajů.

>[!NOTE]
> Pokud jste partner zaregistrovali v síti partnerů Microsoftu, máte nárok na zakoupení Premier Support pro partnery. V opačném případě musíte zakoupit jednu z oprávněných nabídek podpory uvedených výše.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Instalace rozšíření Microsoft Security Code Analysis Extension

1. Po sdílení rozšíření s vaší organizací Azure DevOps můžete přejít na stránku organizace Azure DevOps. Ukázková adresa URL pro takovou stránku je http://dev.azure.com/contoso.
1. Vyberte ikonu nákupního balíčku v pravém horním rohu vedle svého jména a pak vyberte **Spravovat rozšíření**.
1. Vyberte rozšíření Microsoft Security Code Analysis Extension a pak otevřete Průvodce uživatelským rozhraním Azure DevOps a spusťte instalaci.
1. V rozevíracím seznamu vyberte organizaci Azure DevOps, na kterou chcete rozšíření nainstalovat.
1. Vyberte **Install** (Nainstalovat). Po dokončení instalace můžete začít používat rozšíření.

>[!NOTE]
> I v případě, že nemáte přístup pro instalaci rozšíření, pokračujte v postupu instalace. Během procesu instalace můžete požádat o přístup správce vaší organizace Azure DevOps.

Po instalaci rozšíření budou úkoly pro bezpečné vytváření sestavení viditelné a dostupné pro přidání do kanálů Azure.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Přidání konkrétních úloh sestavení do kanálu Azure DevOps

1. Z vaší organizace Azure DevOps otevřete týmový projekt.
1. Vyberte**sestavení** **kanálů** > .
1. Vyberte kanál, do kterého chcete přidat úlohy sestavení rozšíření:
   - Nový kanál: Vyberte **Nový** a postupujte podle podrobných pokynů pro vytvoření nového kanálu.
   - Upravit kanál: Vyberte existující kanál a pak vyberte **Upravit** . tím zahájíte úpravy kanálu.
1. Vyberte **+** a přejít do podokna **Přidat úlohy** .
1. Ze seznamu nebo pomocí vyhledávacího pole vyhledejte úkol sestavení, který chcete přidat. Vyberte **Přidat**.
1. Zadejte parametry potřebné pro úlohu.
1. Zařadit nové sestavení do fronty
   >[!NOTE]
   >Cesty k souborům a složkám jsou relativní vzhledem ke kořenu zdrojového úložiště. Zadáte-li výstupní soubory a složky jako parametry, budou nahrazeny běžným umístěním, které jsme definovali v agentovi sestavení.

> [!TIP]
>
> - Chcete-li po sestavení spustit analýzu, umístěte úkoly sestavení analýzy kódu zabezpečení společnosti Microsoft po kroku sestavení artefaktů sestavení. Tímto způsobem vaše sestavení může dokončit a vystavit výsledky před spuštěním nástrojů statické analýzy.
> - Pro úlohy sestavení s bezpečným vývojem vždy vyberte možnost **pokračovat při chybě** . I když jeden nástroj nefunguje, můžou ostatní spustit. Mezi nástroji neexistují žádné vzájemné závislosti.
> - Úkoly sestavení analýzy kódu zabezpečení společnosti Microsoft selžou pouze v případě, že se nepodaří úspěšně spustit nástroj. Ale jsou úspěšné i v případě, že nástroj identifikuje problémy v kódu. Pomocí úlohy sestavení po analýze můžete sestavení nakonfigurovat tak, aby nebylo úspěšné, když nástroj identifikuje problémy v kódu.
> - Některé úlohy sestavení Azure DevOps se při spuštění prostřednictvím kanálu vydání nepodporují. Konkrétně Azure DevOps nepodporuje úlohy, které publikují artefakty v rámci kanálu vydání.
> - Seznam předdefinovaných proměnných ve službě Azure DevOps Team Build, které můžete zadat jako parametry, najdete v tématu [proměnné sestavení Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Další postup

Další informace o konfiguraci úloh sestavení najdete v našem [Průvodci konfigurací](security-code-analysis-customize.md).

Pokud máte další dotazy týkající se rozšíření a nabízených nástrojů, podívejte se na naše [Časté stránky Nejčastější dotazy](security-code-analysis-faq.md).

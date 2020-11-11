---
title: Průvodce registrací Microsoft Security Code Analysis
description: Naučte se, jak připojit a nainstalovat rozšíření pro analýzu kódu Microsoft Security. Viz předpoklady a zobrazení dalších prostředků.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4fe13c418452a7a88dcd97939d6e853039f3fb64
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517066"
---
# <a name="onboarding-and-installing"></a>Onboarding a instalace

Požadavky na Začínáme s analýzou kódu zabezpečení Microsoftu:

- Opravňující nabídka Microsoft Unified Support, jak je popsáno v následující části.
- Organizace Azure DevOps.
- Oprávnění k instalaci rozšíření do organizace Azure DevOps.
- Zdrojový kód, který lze synchronizovat s kanálem Azure DevOps hostovaným v cloudu.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Připojování rozšíření Microsoft Security Code Analysis Extension

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Uvažujete o nákupu rozšíření pro analýzu kódu zabezpečení společnosti Microsoft?

Pokud máte jednu z následujících nabídek podpory, požádejte svého technického manažera o zakoupení nebo prohození stávajících hodin za účelem získání přístupu k rozšíření:

- Rozšířená vrstva sjednocené podpory
- Úroveň výkonu sjednocené podpory
- Premier Support pro vývojáře
- Premier Support pro partnery
- Premier Support pro podniky

Pokud nemáte některou z výše uvedených smluv o podpoře, můžete rozšíření koupit od některého z našich partnerů.

**Další kroky:**

Pokud splňujete výše uvedené kvalifikace, obraťte se na partnera z níže uvedeného seznamu a Zakupte si rozšíření Microsoft Code Analysis Security. V opačném případě kontaktujte [podporu analýzy kódu zabezpečení společnosti Microsoft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request).

>**Její**

- Zóny – kontaktní údaje: cloudsupport@zones.com
- Wortell – kontaktní údaje: info@wortell.nl
- Logické informace – kontaktní údaje: logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>Stát se partnerem

Tým Microsoft Security Code Analysis je, aby se připojil k partnerům Premier Support Partnerská smlouva. Partneři vám pomůžou zajistit bezpečnější vývoj tohoto rozšíření pro zákazníky, kteří si ho chtějí koupit, ale nemají smlouvu Enterprise support s Microsoftem. Zúčastnění partneři se můžou zaregistrovat [tady](http://www.microsoftpartnersupport.com/msrd/opin).

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Instalace rozšíření Microsoft Security Code Analysis Extension

1. Po sdílení rozšíření s vaší organizací Azure DevOps můžete přejít na stránku organizace Azure DevOps. Ukázková adresa URL pro takovou stránku je `https://dev.azure.com/contoso` .
1. Vyberte ikonu nákupního balíčku v pravém horním rohu vedle svého jména a pak vyberte **Spravovat rozšíření**.
1. Vyberte **Shared**.
1. Vyberte rozšíření Microsoft Security Code Analysis, vyberte **nainstalovat**.
1. V rozevíracím seznamu vyberte organizaci Azure DevOps, na kterou chcete rozšíření nainstalovat.
1. Vyberte **Install** (Nainstalovat). Po dokončení instalace můžete začít používat rozšíření.

>[!NOTE]
> I v případě, že nemáte přístup pro instalaci rozšíření, pokračujte v postupu instalace. Během procesu instalace můžete požádat o přístup správce vaší organizace Azure DevOps.

Po instalaci rozšíření jsou úkoly sestavení pro bezpečné nasazení viditelné a dostupné pro přidání do Azure Pipelines.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Přidání konkrétních úloh sestavení do kanálu Azure DevOps

1. Z vaší organizace Azure DevOps otevřete týmový projekt.
1. Vyberte **Pipelines**  >  **sestavení** kanálů.
1. Vyberte kanál, do kterého chcete přidat úlohy sestavení rozšíření:
   - Nový kanál: vyberte **Nový** a pomocí podrobných kroků vytvořte nový kanál.
   - Upravit kanál: Vyberte existující kanál a pak výběrem **Upravit** zahajte úpravy kanálu.
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
> - Seznam předdefinovaných proměnných ve službě Azure DevOps Team Build, které můžete zadat jako parametry, najdete v tématu [proměnné sestavení Azure DevOps](/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci úloh sestavení najdete v našem průvodci [konfigurací](security-code-analysis-customize.md) nebo v [příručce konfigurace YAML](yaml-configuration.md).

Pokud máte další dotazy týkající se rozšíření a nabízených nástrojů, podívejte se na naše [Časté stránky Nejčastější dotazy](security-code-analysis-faq.md).
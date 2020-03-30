---
title: Příručka pro registraci analýzy bezpečnostního kódu společnosti Microsoft
description: Tento článek popisuje instalaci rozšíření Microsoft Security Code Analysis
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
ms.openlocfilehash: 6132aab98cc8145cb99cf153c64f20fbac00131c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197001"
---
# <a name="onboarding-and-installing"></a>Registrace a instalace

Předpoklady pro zahájení analýzy bezpečnostního kódu společnosti Microsoft:

- Způsobilá nabídka sjednocené podpory společnosti Microsoft, jak je podrobně popsáno v následující části.
- Organizace Azure DevOps.
- Oprávnění k instalaci rozšíření pro organizaci Azure DevOps.
- Zdrojový kód, který lze synchronizovat do kanálu Azure DevOps hostovaného v cloudu.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Registrace rozšíření Microsoft Security Code Analysis

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Máte zájem o zakoupení rozšíření Microsoft Security Code Analysis?

Pokud máte některou z následujících nabídek podpory, obraťte se na svého technického správce účtu a zakupte nebo vyměníte stávající hodiny, abyste získali přístup k rozšíření:

- Pokročilá úroveň sjednocené podpory
- Úroveň výkonu sjednocené podpory
- Premier Podpora pro vývojáře
- Premier Podpora pro partnery
- Premier Podpora pro podniky

Pokud nemáte některou z výše uvedených smluv o podpoře, můžete si rozšíření zakoupit u jednoho z našich partnerů.

**Další kroky:**

Obraťte se na partnera z níže uvedeného seznamu a požádejte o zakoupení rozšíření Microsoft Security Code Analysis.

>**Partnery:**

- Zóny - Kontaktní údaje:cloudsupport@zones.com

### <a name="become-a-partner"></a>Staňte se partnerem

Tým microsoft security code analysis hledá partnery s dohodou Premier Support for Partners. Partneři pomohou zákazníkům Azure DevOps bezpečněji vyvíjet tím, že rozšíření prodají zákazníkům, kteří si ho chtějí koupit, ale nemají smlouvu o podnikové podpoře se společností Microsoft. Zájemci se mohou zaregistrovat [zde](http://www.microsoftpartnersupport.com/msrd/opin).

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Instalace rozšíření Microsoft Security Code Analysis

1. Po sdílení rozšíření s vaší organizací Azure DevOps přejděte na stránku organizace Azure DevOps. Příklad adresy URL pro `https://dev.azure.com/contoso`takovou stránku je .
1. V pravém horním rohu vedle svého jména vyberte ikonu nákupní **tašky**a pak vyberte Spravovat rozšíření .
1. Vyberte **Sdílené**.
1. Vyberte rozšíření Microsoft Security Code Analysis, vyberte **nainstalovat**.
1. V rozevíracím seznamu zvolte organizaci Azure DevOps, na kterou chcete rozšíření nainstalovat.
1. Vyberte **Install** (Nainstalovat). Po dokončení instalace můžete začít používat rozšíření.

>[!NOTE]
> I v případě, že nemáte přístup k instalaci rozšíření, pokračujte kroky instalace. Během procesu instalace můžete požádat o přístup od správce organizace Azure DevOps.

Po instalaci rozšíření jsou viditelné úlohy sestavení zabezpečeného vývoje, které jsou viditelné a dostupné pro přidání do vašich azure kanálů.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Přidání konkrétních úloh sestavení do kanálu Azure DevOps

1. V organizaci Azure DevOps otevřete týmový projekt.
1. Vyberte **sestavení kanálů** > **.**
1. Vyberte kanál, do kterého chcete přidat úlohy sestavení rozšíření:
   - Nový kanál: Vyberte **Nový** a podle podrobných kroků vytvořte nový kanál.
   - Upravit kanál: Vyberte existující kanál a pak vyberte **Upravit,** abyste začali kanál upravovat.
1. Vyberte **+** podokno **Přidat úkoly** a přejděte do něj.
1. Ze seznamu nebo pomocí vyhledávacího pole najděte úlohu sestavení, kterou chcete přidat. Vyberte **Přidat**.
1. Zadejte parametry potřebné pro úlohu.
1. Zařazování nového sestavení do fronty.
   >[!NOTE]
   >Cesty k souborům a složkám jsou relativní ke kořenovému adresáři zdrojového úložiště. Pokud zadáte výstupní soubory a složky jako parametry, budou nahrazeny společným umístěním, které jsme definovali v agentovi sestavení.

> [!TIP]
>
> - To run an analysis after your build, place the Microsoft Security Code Analysis build tasks after the Publish Build Artifacts step of your build. Tímto způsobem vaše sestavení můžete dokončit a zaúčtovat výsledky před spuštěním statické analýzy nástroje.
> - Vždy vyberte **pokračovat na chybu** pro zabezpečené vývojové úlohy sestavení. I když jeden nástroj selže, ostatní mohou běžet. Mezi nástroji neexistují žádné vzájemné závislosti.
> - Úlohy sestavení analýzy kódu zabezpečení společnosti Microsoft se nezdaří pouze v případě, že se nástroj úspěšně spustí. Ale úspěšné i v případě, že nástroj identifikuje problémy v kódu. Pomocí úlohy sestavení po analýze můžete nakonfigurovat sestavení tak, aby se nezdaří, když nástroj identifikuje problémy v kódu.
> - Některé úlohy sestavení Azure DevOps nejsou podporovány při spuštění prostřednictvím kanálu vydání. Přesněji řečeno Azure DevOps nepodporuje úkoly, které publikují artefakty z kanálu vydání.
> - Seznam předdefinovaných proměnných v azure devops team buildu, který můžete zadat jako parametry, najdete v [tématu Azure DevOps Build Variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci úloh sestavení naleznete v [příručce konfigurace](security-code-analysis-customize.md) nebo [v průvodci konfigurací YAML](yaml-configuration.md).

Máte-li další dotazy týkající se rozšíření a nabízených nástrojů, podívejte se na naši [stránku faq](security-code-analysis-faq.md).

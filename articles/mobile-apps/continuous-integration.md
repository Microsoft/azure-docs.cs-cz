---
title: Automatizace životního cyklu vašich aplikací pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je App Center, které pomáhají nastavit průběžné sestavování a integraci pro vaše mobilní aplikace.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795546"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizace životního cyklu vašich aplikací pomocí průběžného sestavování a integrace

Jako vývojáři napíšete kód a vrátíte se do úložiště kódu, ale potvrzení vrácená do úložiště nemusí být vždy konzistentní. U více vývojářů pracujících na stejném projektu, problémy s integrací a tým by mohl běžet do situací, kdy věci nefungují, chyby se udržují piling a vývoj projektů se bude zpozdit. Vývojáři musí počkat, než se sestaví a otestuje celý softwarový kód, aby se kontrolovaly chyby, a tento proces je pomalý a méně iterativní. 

Díky **průběžnému sestavování a integraci**mohou vývojáři zjednodušit sestavení a testování kódu tím, že nepotvrzují změny pouze v úložišti zdrojového kódu, ale také zavedou testy a ověření do prostředí sestavení, aby byly vždy spuštěny. Testuje kód. Všechny změny provedené ve zdrojovém kódu se průběžně sestavují vždy, když dojde k potvrzení změn v úložišti. Při každém vrácení se změnami Server CI ověří a spustí jakýkoliv test, který vývojář vytvořil. Pokud testy neprojde, kód se pošle zpátky a provede další změny. To umožňuje vývojáři Nerušit sestavení, která jsou vytvořena, a nespouštět všechny testy místně na svém počítači, což zase zvyšuje produktivitu vývojářů. 

## <a name="key-benefits"></a>Klíčové výhody
- **Automatické** sestavení, testování a nasazení kanálu.
- **Odhalte chyby a vyřešte problémy** včas a zajistěte rychlejší tarify verzí.
- Pravidelně **potvrzování kódu** a rychlé sestavování aplikací.
- **Flexibilita** pro rychlé změny kódu bez problémů.
- **Rychlejší uvedení na trh** zajistí, že celým způsobem zajišťuje pouze kvalitní kód.
- **Malé změny kódu** , protože umožňují integraci malých částí kódu najednou.
- **Zvýšení transparentnosti a zodpovědnosti týmu** vám umožní získat **nepřetržitou zpětnou vazbu** nejen od zákazníků, ale i vašeho týmu.

Pomocí následujících služeb můžete v mobilních aplikacích povolit kanál nepřetržité integrace.

## <a name="visual-studio-app-center"></a>Centrum aplikací Visual Studio
Služba [App Center Build](/appcenter/build/) Service pomáhá sestavovat nativní aplikace a aplikace pro různé platformy, na kterých tým pracuje, pomocí zabezpečené cloudové infrastruktury. Své úložiště můžete snadno připojit v App Center a začít sestavovat aplikaci v cloudu při každém potvrzení, aniž byste se museli starat o konfiguraci serverů sestavení místně, složitou konfiguraci a kód, který sestaví na počítači spolupracovníka, ale ne na váš.

Díky zvýšení síly služeb App Center můžete pracovní postup dále automatizovat. Automaticky vydávají buildy do testerů a úložišť veřejných aplikací pomocí App Center distribuují nebo spouštějí automatizované testy uživatelského rozhraní na tisících skutečných zařízení a konfigurací operačních systémů v cloudu s App Center testováním.

**Klíčové funkce**
- **Průběžnou integraci si nastavte** během několika minut a Sestavujte aplikace častěji a rychleji.
- Integrujte s **GitHubem, BitBucket, Azure DevOps a GitLab**.
- **Rychlé a zabezpečené sestavení** na spravovaných počítačích hostovaných v cloudu.
- **Umožněte vašim buildům "spustit test"** a ověřte, jestli je aplikace sestavená na zařízeních s iOS a Androidem v reálném světě.
- **Nativní podpora a podpora pro různé platformy** – iOS, Android, MacOS, Windows, Xamarin, reagují na nativní.
- **Přizpůsobte sestavení** tak, že přidáte skripty po klonování, předběžném sestavení a po sestavení.

**Odkazy**
- [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)služby v Azure DevOps je plně funkční služba průběžné integrace (CI) a průběžné doručování (CD), která spolupracuje s vaším preferovaným poskytovatelem Gitu a kterou můžete nasadit do většiny hlavních cloudových služeb, včetně Azure. Můžete začít s vaším kódem na webu GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud nebo Azure Repos. Potom můžete automatizovat sestavení, testování a nasazení kódu pro Microsoft Azure, Google Cloud Platform nebo Amazon Web Services.

**Klíčové funkce**
- Zjednodušené prostředí založené na úlohách pro nastavení serveru CI pro nativní mobilní aplikace (Android, iOS a Windows) i pro různé platformy (Xamarin, Cordova a reakci na nativní), kromě Microsoft a jiných společností než Microsoft (Node. js, Java) technik.
- **Jakýkoli jazyk, platformu a Cloud** – sestavení, testování a nasazení aplikací Node. js, Python, Java, php, Ruby, přejít, C/C++, C#, Android a iOS. Využívejte souběžné spouštění v Linuxu, macOS a Windows. Nasaďte je do cloudových poskytovatelů, jako je Azure, AWS a GCP. Distribuujte mobilní aplikace přes beta kanály a obchody s aplikacemi.
- **Podpora nativního kontejneru** – snadno vytvářejte nové kontejnery a nahrajte je do libovolného registru. Nasaďte kontejnery na nezávislé hostitele nebo Kubernetes.
- **Pokročilé pracovní postupy** – snadné řetězení sestavení a vícenásobná sestavení. Podpora YAML, integrace testů, bran vydaných verzí, vytváření sestav a dalších.
- **Rozšiřitelné** – použijte řadu úloh sestavení, testování a nasazení vytvořených komunitou – stovky rozšíření od časové rezervy až po SonarCloud. Můžete dokonce nasadit z jiných systémů CI, jako je Jenkinse, a pomocí webhooků a rozhraní REST API, které vám pomohou integrovat
- **Bezplatné buildy hostované v cloudu** pro veřejná a soukromá úložiště.
- **Podporuje nasazení na jiné dodavatele cloudu** , jako je Amazon Web Services, Google Cloud Platform atd.

**Odkazy**
- [Začínáme s průvodcem pro Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Začínáme s Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Rychlé starty](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

V pořadí zvolit správnou službu pro sestavení vaší aplikace postupujte podle článku, který porovnává [App Center Build vs. Azure Pipelines](/appcenter/build/choose-between-services).

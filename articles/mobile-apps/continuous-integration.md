---
title: Automatizace životního cyklu vašich aplikací pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je App Center, které vám pomůžou nastavit průběžné sestavování a integraci pro vaše mobilní aplikace.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240929"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizace životního cyklu vašich aplikací pomocí průběžného sestavování a integrace

Jako vývojáři napíšete kód a zkontrolujete ho do úložiště kódu, ale potvrzení vrácená do úložiště nemusí být vždycky konzistentní. Když v jednom projektu pracuje více vývojářů, můžou se problémy s integrací. Týmy můžou běžet do situací, kdy věci nebudou fungovat, chyby a vývoj projektů se bude zpozdit. Vývojáři musí počkat, než se sestaví a otestuje celý softwarový kód, aby kontroloval chyby, což znamená, že proces bude pomalý a méně iterativní. 

Díky průběžnému sestavování a integraci můžou vývojáři zjednodušit sestavení a testovat svůj kód tím, že zapotvrzují změny do úložiště zdrojového kódu a zavedou testy a ověření do prostředí sestavení. Tímto způsobem vždy spouští testy pro svůj kód. Všechny změny provedené ve zdrojovém kódu jsou vytvořeny průběžně vždy, když dojde k potvrzení změn v úložišti. Při každém vrácení se změnami ověří server průběžné integrace (CI) a provede libovolný test, který vývojář vytvořil. Pokud testy neprojde, kód se pošle zpátky a provede další změny. Tímto způsobem vývojáři neruší sestavení, která jsou vytvořena. Nemusí také spouštět všechny testy místně na svých počítačích, což zvyšuje produktivitu vývojářů. 

## <a name="key-benefits"></a>Klíčové výhody
- Automatizujte sestavení, testy a nasazení pro kanály.
- Odhalte chyby a vyřešte problémy včas a zajistěte rychlejší tarify verzí.
- Pravidelně potvrzování kódu a rychlé sestavování aplikací.
- Získejte flexibilitu pro rychlé změny kódu bez problémů.
- Využijte rychlejšího uvedení na trh, aby bylo vše v dobré kvalitě.
- Rychlejší provádění změn malého kódu, protože malé části kódu jsou integrovány současně.
- Zvyšte transparentnost a zodpovědnost týmu, abyste získali nepřetržitou zpětnou vazbu od zákazníků a týmu.

Pomocí následujících služeb můžete v mobilních aplikacích povolit kanál nepřetržité integrace.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Build](/appcenter/build/) pomáhá sestavovat nativní aplikace a aplikace pro různé platformy, na kterých tým pracuje, pomocí zabezpečené cloudové infrastruktury. Své úložiště můžete snadno připojit v Visual Studio App Center a začít sestavovat aplikaci v cloudu při každém potvrzení. Nemusíte si dělat starosti s konfigurací serverů sestavení místně, složitých konfigurací a kódu, který sestaví na počítači spolupracovníka, ale ne na vašem.

Díky zvýšení síly služeb Visual Studio App Center můžete pracovní postup dále automatizovat. Sestavení můžete automaticky vydávat do testerů a úložišť veřejných aplikací pomocí App Center distribuovat. Pomocí App Centerho testu můžete také spouštět automatizované testy uživatelského rozhraní na tisících reálných konfigurací zařízení a operačních systémů v cloudu.

**Klíčové funkce**
- Nastavte průběžnou integraci během několika minut a Sestavujte aplikace častěji a rychleji.
- Integraci s GitHubem, BitBucket, Azure DevOps a GitLab.
- Vytvářejte rychlé a zabezpečené buildy na spravovaných počítačích hostovaných v cloudu.
- Umožněte vašim buildům spustit test a ověřte, jestli je aplikace sestavená na zařízeních s iOS a Androidem v reálném světě.
- Získejte nativní podporu a podporu pro víc platforem pro iOS, Android, macOS, Windows, Xamarin a reakci na nativní.
- Přizpůsobte sestavení tak, že přidáte skripty po klonování, předběžném sestavení a po sestavení.

**Odkazy**
- [Zaregistrujte se Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)služby v Azure DevOps je plně funkční služba pro průběžnou integraci a průběžné doručování (CD), která spolupracuje s vaším preferovaným poskytovatelem Git. Může se nasadit do většiny hlavních cloudových služeb, které zahrnují Azure. Můžete začít s vaším kódem na webu GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud nebo Azure Repos. Potom můžete automatizovat sestavení, testování a nasazení kódu pro Microsoft Azure, Google Cloud Platform nebo Amazon Web Services (AWS).

**Klíčové funkce**
- **Zjednodušené prostředí založené na úlohách pro nastavení serveru CI:** Nastavte server CI pro nativní mobilní aplikace (Android, iOS a Windows) i pro různé platformy (Xamarin, Cordova a reakci na nativní), a to i na serverové technologie od Microsoftu a jiných společností než Microsoft (Node. js, Java).
- **Libovolný jazyk, platforma a Cloud:** Sestavujte, testujte a nasaďte aplikace Node. js, Python, Java, PHP, Ruby, přejít, C/C++, C#, Android a iOS. Spouštějte paralelně na systémech Linux, macOS a Windows. Nasaďte je do cloudových poskytovatelů, jako je Azure, AWS a Google Cloud Platform. Distribuujte mobilní aplikace přes beta kanály a obchody s aplikacemi.
- **Podpora nativního kontejneru:** Vytvářejte nové kontejnery snadno a nahrajte je do libovolného registru. Nasaďte kontejnery na nezávislé hostitele nebo Kubernetes.
- **Pokročilé pracovní postupy:** Snadné vytváření řetězů sestavení a mezifází sestavení. Získejte podporu pro YAML, integraci testů, brány vydaných verzí, generování sestav a další.
- **Rozšiřitelný:** Použijte rozsah úloh sestavení, testování a nasazení vytvořených komunitou, což zahrnuje stovky rozšíření od časové rezervy až po SonarCloud. Můžete dokonce nasadit z jiných systémů CI, jako je Jenkinse. Webové háky a rozhraní REST API vám můžou přispět k integraci.
- **Bezplatná sestavení hostovaná v cloudu:** Tato sestavení jsou k dispozici pro veřejná a soukromá úložiště.
- **Podpora nasazení na jiné dodavatele cloudu:** Dodavatelé zahrnují AWS a Google Cloud Platform.

**Odkazy**
- [Začínáme s průvodcem pro Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Začínáme s Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Rychlé starty](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Pro usnadnění výběru správné služby pro sestavení vaší aplikace si přečtěte článek, který porovnává [App Center Build vs. Azure Pipelines](/appcenter/build/choose-between-services).

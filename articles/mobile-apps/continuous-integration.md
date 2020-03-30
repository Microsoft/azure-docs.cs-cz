---
title: Automatizace životního cyklu aplikací pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je Centrum aplikací, které pomáhají nastavit průběžné vytváření a integraci pro vaše mobilní aplikace.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240929"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizace životního cyklu svých aplikací díky průběžnému vytváření a integraci

Jako vývojáři napíšete kód a vrátíte jej do úložiště kódu, ale revize zadávané do úložiště nemusí být vždy konzistentní. Když na stejném projektu pracuje více vývojářů, mohou problémy přijít s integrací. Týmy se mohou dostat do situací, kdy věci nefungují, chyby se hromadí a vývoj projektu se zpozdí. Vývojáři musí počkat, až celý softwarový kód je sestaven a testovány pro kontrolu chyb, což činí proces pomalý a méně iterativní. 

Díky průběžnému vytváření a integraci mohou vývojáři zjednodušit sestavení a testovat svůj kód potvrzením jejich změn v úložišti zdrojového kódu a vložením testů a ověření do prostředí sestavení. Tímto způsobem jsou vždy spuštěny testy proti jejich kódu. Všechny změny provedené ve zdrojovém kódu jsou sestaveny nepřetržitě vždy, když dojde k potvrzení do úložiště. Při každém vrácení se změnami server průběžné integrace (CI) ověří a provede jakýkoli test, který vývojář vytvořil. Pokud testy neprojdou, kód je odeslán zpět pro další změny. Tímto způsobem vývojáři neporušují sestavení, které jsou vytvořeny. Také nemusí spouštět všechny testy místně na svých počítačích, což zvyšuje produktivitu vývojářů. 

## <a name="key-benefits"></a>Klíčové výhody
- Automatizujte sestavení, testy a nasazení pro kanály.
- Detekujte chyby a opravte problémy včas, abyste zajistili rychlejší rychlost uvolňování.
- Posuzujte kód častěji a rychle vytvářejte aplikace.
- Získejte flexibilitu pro rychlou změnu kódu bez problémů.
- Získejte rychlejší čas na trh, takže pouze kvalitní kód je celou cestu.
- Proveďte malé změny kódu efektivněji, protože malé části kódu jsou integrovány najednou.
- Zvyšte transparentnost a odpovědnost týmu, abyste získali průběžnou zpětnou vazbu od svých zákazníků a svého týmu.

Pomocí následujících služeb můžete v mobilních aplikacích povolit kanál průběžné integrace.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Build](/appcenter/build/) vám pomůže vytvářet nativní a multiplatformní aplikace, na kterých váš tým pracuje, pomocí zabezpečené cloudové infrastruktury. Repo můžete snadno připojit v Centru aplikací Visual Studia a začít vytvářet aplikaci v cloudu při každé revizi. Nemusíte se starat o konfiguraci serverů sestavení místně, složité konfigurace a kód, který staví na počítači spolupracovníka, ale ne vy.

S přidanou výkon služby Visual Studio App Center, můžete dále automatizovat pracovní postup. Pomocí aplikace App Center Distribute můžete automaticky vydávat sestavení testerům a veřejným obchodům s aplikacemi. Pomocí testu Centra aplikací můžete také spouštět automatizované testy ui na tisících skutečných konfigurací zařízení a operačního systému v cloudu.

**Klíčové funkce**
- Nastavte průběžnou integraci během několika minut a vytvářejte aplikace častěji a rychleji.
- Integrujte s GitHub, BitBucket, Azure DevOps a GitLab.
- Vytvářejte rychlé a bezpečné sestavení na spravovaných počítačích hostovaných v cloudu.
- Umožněte svým sestavením spustit test a ověřte, jestli se aplikace buduje v reálných zařízeních se systémem iOS a Android.
- Získejte nativní a multiplatformní podporu pro iOS, Android, macOS, Windows, Xamarin a React Native.
- Přizpůsobte si sestavení přidáním skriptů post-clone, pre-build a post-build.

**Odkazy**
- [Registrace pomocí Centra aplikací Visual Studia](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme se sestavením Centra aplikací](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), služba v Azure DevOps, je plně vybavená služba průběžné integrace a průběžného doručování (CD), která spolupracuje s vaším preferovaným poskytovatelem Gitu. Může se nasazovat do většiny hlavních cloudových služeb, které zahrnují Azure. Svůj kód můžete začít na GitHubu, GitHub Enterprise Server, GitLabu, Bitbucket Cloudu nebo Azure Repos. Potom můžete automatizovat sestavení, testování a nasazení kódu do Microsoft Azure, Google Cloud Platform nebo Amazon Web Services (AWS).

**Klíčové funkce**
- **Zjednodušené prostředí založené na úlohách pro nastavení serveru CI:** Nastavte server CI pro nativní (Android, iOS a Windows) i pro mobilní aplikace napříč platformami (Xamarin, Cordova a React Native) kromě serverových technologií microsoftu a jiných společností (Node.js, Java).
- **Libovolný jazyk, platforma a cloud:** Vytvářejte, testujte a nasazujte aplikace Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android a iOS. Spouštět paralelně na Linuxu, macOS a Windows. Nasazujte do poskytovatelů cloudu, jako jsou Azure, AWS a Google Cloud Platform. Distribuujte mobilní aplikace prostřednictvím beta kanálů a obchodů s aplikacemi.
- **Podpora nativního kontejneru:** Snadno vytvářejte nové kontejnery a přesouvejte je do libovolného registru. Nasazujte kontejnery na nezávislé hostitele nebo Kubernetes.
- **Pokročilé pracovní postupy:** Snadno vytvářejte řetězy sestavení a vícefázové sestavy. Získejte podporu pro YAML, integraci testů, brány vydání, vytváření sestav a další.
- **Rozšiřitelné:** Použijte celou řadu úloh sestavení, testování a nasazení vytvořených komunitou, která zahrnuje stovky rozšíření od Slacku po SonarCloud. Můžete dokonce nasadit z jiných systémů CI, jako je Jenkins. Webové háčky a REST API vám mohou pomoci integrovat.
- **Bezplatná cloudová sestavení:** Tato sestavení jsou k dispozici pro veřejné a soukromé úložiště.
- **Podpora pro nasazení pro jiné dodavatele cloudu:** Mezi dodavatele patří AWS a Google Cloud Platform.

**Odkazy**
- [Začínáme s průvodcem Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Začínáme s Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Rychlé starty](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Pokud vám pomoct vybrat správnou službu pro sestavení aplikací, přečtěte si článek, který porovnává [sestavení Centra aplikací vs. Azure Pipelines](/appcenter/build/choose-between-services).

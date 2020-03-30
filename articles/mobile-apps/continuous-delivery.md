---
title: Automatizace nasazení a vydání mobilních aplikací pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je Centrum aplikací, které pomáhají nastavit kanál průběžného doručování pro vaše mobilní aplikace.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235341"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizujte nasazení a vydávání mobilních aplikací pomocí nepřetržitých doručovacích služeb

Jako vývojáři napíšete kód a vrátíte jej do úložiště kódu, ale revize zadávané do úložiště nemusí být vždy konzistentní. Když na stejném projektu pracuje více vývojářů, mohou problémy přijít s integrací. Týmy se mohou dostat do situací, kdy věci nefungují, chyby se hromadí a vývoj projektu se zpozdí. Vývojáři musí počkat, až celý softwarový kód je sestaven a testovány pro kontrolu chyb, což činí proces pomalý a méně iterativní.

Díky nepřetržitému doručování můžete automatizovat nasazení a vydávání mobilních aplikací. Nezáleží na tom, zda distribuujete aplikaci skupině testerů nebo zaměstnanců společnosti (pro testování beta) nebo do obchodu s aplikacemi (pro produkční prostředí). Díky nepřetržitému doručování je nasazení méně riskantní a podporuje rychlé iterace. Můžete také průběžně vydávat nové změny svým zákazníkům.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuce binárních souborů aplikace beta testerům
Beta testování mobilní aplikace je jedním z kritických kroků během procesu vývoje aplikací. Pomáhá najít chyby a problémy ve vaší aplikaci brzy. Zpětná vazba zlepšuje kvalitu aplikace, když ji připravujete pro produkční použití.

Pomocí následujících služeb můžete v mobilních aplikacích povolit kanál průběžného doručování.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[Distribuce center aplikací](/appcenter/distribution/) je nástroj pro vývojáře, kteří rychle vydávají sestavení do zařízení. Díky kompletnímu prostředí instalačního portálu je Distribuce App Center výkonným řešením pro distribuci beta testerů aplikací. Je to také vhodná alternativa k distribuci prostřednictvím veřejných obchodů s aplikacemi. Vývojáři mohou ještě více automatizovat svůj distribuční pracovní postup pomocí buildu Centra aplikací a integrace veřejného úložiště aplikací.

**Klíčové funkce**
- Distribuujte aplikaci beta testerům a uživatelům a ujistěte se, že všichni vaši testeři mají nejnovější verzi aplikace.
- Upozorněte testery na nové verze, aniž by testeři znovu procházeli tokem stahování.
- Spravujte distribuční skupiny pro různé verze aplikace.
- Distribuce do obchodů: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Získejte podporu platformy pro iOS, Android, macOS, tvOS, Xamarin, React Native, Unity a Cordova.
- Automaticky zaregistrujte zařízení iOS do svého zřizovacího profilu.

**Odkazy**
- [Registrace pomocí Centra aplikací Visual Studia](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s aplikací Distribuce centra aplikací](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) je plně vybavená služba průběžné integrace (CI) a průběžného doručování (CD), která spolupracuje s vaším preferovaným poskytovatelem Gitu. Azure Pipelines se můžou nasadit do většiny hlavních cloudových služeb, jako jsou služby Azure. Svůj kód můžete začít na GitHubu, GitHub Enterprise Server, GitLabu, Bitbucket Cloudu nebo Azure Repos. Potom můžete automatizovat sestavení, testování a nasazení kódu do Microsoft Azure, Google Cloud Platform nebo Amazon Web Services (AWS).

**Klíčové funkce**
- **Zjednodušené prostředí založené na úlohách pro nastavení serveru CI:** Nastavte ci server pro nativní (Android, iOS a Windows) i pro mobilní aplikace napříč platformami (Xamarin, Cordova a React Native).
- **Libovolný jazyk, platforma a cloud:** Vytvářejte, testujte a nasazujte aplikace Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android a iOS. Spouštět paralelně na Linuxu, macOS a Windows. Nasazujte do poskytovatelů cloudu, jako jsou Azure, AWS a Google Cloud Platform. Distribuujte mobilní aplikace prostřednictvím beta kanálů a obchodů s aplikacemi.
- **Podpora nativního kontejneru:** Snadno vytvářejte nové kontejnery a přesouvejte je do libovolného registru. Nasazujte kontejnery na nezávislé hostitele nebo Kubernetes.
- **Pokročilé pracovní postupy a funkce:** Snadno vytvářejte řetězy sestavení a vícefázové sestavy. Získejte podporu pro YAML, integraci testů, brány vydání, vytváření sestav a další.
- **Rozšiřitelné:** Použijte celou řadu úloh sestavení, testování a nasazení vytvořených komunitou, která zahrnuje stovky rozšíření od Slacku po SonarCloud. Můžete dokonce nasadit z jiných systémů CI, jako je Jenkins. Webové háčky a REST API vám mohou pomoci integrovat.
- **Bezplatná cloudová sestavení:** Tato sestavení jsou k dispozici pro veřejné a soukromé úložiště.
- **Podpora pro nasazení pro jiné dodavatele cloudu:** Mezi dodavatele patří AWS a Google Cloud Platform.

**Odkazy**
- [Začínáme s průvodcem Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Začínáme s Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuce aplikace přímo do Obchodů s aplikacemi
Poté, co je vaše aplikace připravena k produkčnímu použití a chcete, aby byla použita veřejně, musí být odeslána do obchodů s aplikacemi, kde ji mohou zákazníci stáhnout. Existuje několik způsobů, jak distribuovat aplikaci přímo do obchodů s aplikacemi. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Pomocí [služby Distribuce centra aplikací](/appcenter/distribution/stores/)můžete mobilní aplikace publikovat přímo do obchodů s aplikacemi. Po aplikaci je připraven ke stažení uživateli, můžete publikovat binární soubory aplikace přímo z portálu Visual Studio App Center. 

Můžete přímo distribuovat do:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Obchod Google Play](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
V obchodě s aplikacemi vyvinutém a udržovaným společností Apple mohou uživatelé procházet a stahovat aplikace vyvinuté pro zařízení iOS, MacOS, WatchOS a tvOS. Vývojáři musí své aplikace pro iOS odesílat do Apple App Storu pro veřejné použití.

### <a name="google-play"></a>Google Play

Google Play je oficiální obchod s aplikacemi pro operační systém Android, kde mohou uživatelé procházet a stahovat aplikace vyvinuté pro zařízení Android, které jsou publikovány prostřednictvím společnosti Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) je cloudová služba v prostoru pro správu podnikové mobility, která pomáhá umožnit vašim zaměstnancům produktivitu a zároveň chránit vaše podniková data. Intune vám umožňuje:
- Spravujte mobilní zařízení a počítače, které vaši pracovníci používají pro přístup k firemním datům.
- Spravujte mobilní aplikace, které vaši zaměstnanci používají.
- Chraňte informace o své společnosti tím, že budeš kontrolovat způsob, jakým k nim vaši zaměstnanci přistupují a jak je sdílí.
- Ujistěte se, že zařízení a aplikace splňují požadavky na zabezpečení společnosti.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Nasazení aktualizací přímo do zařízení uživatelů

### <a name="codepush"></a>CodePush
S [CodePush](/appcenter/distribution/codepush/) v App Center mohou vývojáři Apache Cordova a React Native nasazovat aktualizace mobilních aplikací přímo do zařízení svých uživatelů. Funguje jako centrální úložiště, do kterého mohou vývojáři publikovat určité aktualizace, jako je JavaScript, HTML, CSS a změny obrázků. Aplikace pak mohou dotazovat na aktualizace z úložiště pomocí zadaných sad SDK klienta. Tímto způsobem můžete mít více deterministický a přímý model zapojení s uživateli při řešení chyb nebo přidávání malých funkcí. Není nutné znovu sestavit binární soubor nebo jej znovu distribuovat prostřednictvím všech veřejných obchodů s aplikacemi.

**Klíčové funkce**
- Vývojáři Cordova a React Native mohou nasazovat aktualizace mobilních aplikací přímo do zařízení svých uživatelů, aniž by se uvolnili v obchodě.
- Užitečné pro opravu chyb nebo přidání a odebrání malých funkcí, které nevyžadují opětovné sestavení binární a redistribuovat prostřednictvím příslušných obchodů.

**Odkazy**
- [Registrace pomocí Centra aplikací Visual Studia](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s CodePush v App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)
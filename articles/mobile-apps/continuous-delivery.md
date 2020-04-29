---
title: Automatizace nasazení a vydání vašich mobilních aplikací pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je App Center, které vám pomůžou nastavit kanál průběžného doručování pro vaše mobilní aplikace.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235341"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizace nasazení a vydání vašich mobilních aplikací pomocí služeb průběžného doručování

Jako vývojáři napíšete kód a zkontrolujete ho do úložiště kódu, ale potvrzení vrácená do úložiště nemusí být vždycky konzistentní. Když v jednom projektu pracuje více vývojářů, můžou se problémy s integrací. Týmy můžou běžet do situací, kdy věci nebudou fungovat, chyby a vývoj projektů se bude zpozdit. Vývojáři musí počkat, než se sestaví a otestuje celý softwarový kód, aby kontroloval chyby, což znamená, že proces bude pomalý a méně iterativní.

Díky průběžnému doručování automatizujete nasazení a vydání vašich mobilních aplikací. Nezáleží na tom, jestli distribuujete aplikaci do skupiny testerů nebo zaměstnanců společnosti (pro testování beta verzí) nebo do App Storu (pro produkční prostředí). Průběžné doručování způsobuje méně riskantní nasazení a podporuje rychlé iterace. V nepřetržitém případě můžete také uvolnit nové změny zákazníků.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuce binárních souborů aplikace do testerů beta verzí
Testování beta verze vaší mobilní aplikace je jedním z důležitých kroků během procesu vývoje aplikace. Pomáhá včas najít chyby a problémy v aplikaci. Váš názor zvyšuje kvalitu vaší aplikace, když ji připravujete pro použití v produkčním prostředí.

Pomocí následujících služeb můžete v mobilních aplikacích povolit kanál průběžného doručování.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Distributed](/appcenter/distribution/) je nástroj, který vývojářům umožňuje rychle vydávat buildy do zařízení. S kompletním prostředím instalačního portálu je App Center distribuovat výkonné řešení pro distribuci testerů beta aplikací. Je taky vhodná alternativa k distribuci prostřednictvím veřejných obchodů s aplikacemi. Vývojáři můžou svůj pracovní postup distribuce ještě dál automatizovat pomocí App Center integrace s buildem a veřejným úložištěm aplikací.

**Klíčové funkce**
- Distribuujte své aplikace testerům beta verzí a uživatelům a ujistěte se, že jsou všechny testery na nejnovější verzi vaší aplikace.
- Upozorněte testery na nové verze bez testerů, kteří procházejí přes tok stahování.
- Spravujte distribuční skupiny pro různé verze vaší aplikace.
- Distribuovat do úložišť: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Získejte podporu platforem pro iOS, Android, macOS, tvOS, Xamarin, reagují na nativní, Unity a Cordova.
- Automaticky zaregistruje zařízení s iOS do vašeho zřizovacího profilu.

**Odkazy**
- [Zaregistrujte se Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s distribucí App Center](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) je plně funkční služba pro průběžnou integraci (CI) a průběžné doručování (CD), která spolupracuje s vaším preferovaným poskytovatelem Git. Azure Pipelines se můžou nasazovat do většiny hlavních cloudových služeb, jako jsou služby Azure. Můžete začít s vaším kódem na webu GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud nebo Azure Repos. Potom můžete automatizovat sestavení, testování a nasazení kódu pro Microsoft Azure, Google Cloud Platform nebo Amazon Web Services (AWS).

**Klíčové funkce**
- **Zjednodušené prostředí založené na úlohách pro nastavení serveru CI:** Nastavte server CI pro nativní mobilní aplikace (Android, iOS a Windows) i pro různé platformy (Xamarin, Cordova a reakci na nativní).
- **Libovolný jazyk, platforma a Cloud:** Sestavujte, testujte a nasaďte aplikace Node. js, Python, Java, PHP, Ruby, přejít, C/C++, C#, Android a iOS. Spouštějte paralelně na systémech Linux, macOS a Windows. Nasaďte je do cloudových poskytovatelů, jako je Azure, AWS a Google Cloud Platform. Distribuujte mobilní aplikace přes beta kanály a obchody s aplikacemi.
- **Podpora nativního kontejneru:** Vytvářejte nové kontejnery snadno a nahrajte je do libovolného registru. Nasaďte kontejnery na nezávislé hostitele nebo Kubernetes.
- **Pokročilé pracovní postupy a funkce:** Snadné vytváření řetězů sestavení a mezifází sestavení. Získejte podporu pro YAML, integraci testů, brány vydaných verzí, generování sestav a další.
- **Rozšiřitelný:** Použijte rozsah úloh sestavení, testování a nasazení vytvořených komunitou, což zahrnuje stovky rozšíření od časové rezervy až po SonarCloud. Můžete dokonce nasadit z jiných systémů CI, jako je Jenkinse. Webové háky a rozhraní REST API vám můžou přispět k integraci.
- **Bezplatná sestavení hostovaná v cloudu:** Tato sestavení jsou k dispozici pro veřejná a soukromá úložiště.
- **Podpora nasazení na jiné dodavatele cloudu:** Dodavatelé zahrnují AWS a Google Cloud Platform.

**Odkazy**
- [Začínáme s průvodcem pro Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Začínáme s Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuce aplikace přímo do obchodů s aplikacemi
Jakmile je vaše aplikace připravená na použití v produkčním prostředí a chcete ji použít veřejně, je nutné ji odeslat do obchodů s aplikacemi, kde je mohou stahovat zákazníci. Existuje několik způsobů, jak distribuovat aplikaci přímo do obchodů s aplikacemi. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Při [distribuci App Center](/appcenter/distribution/stores/)můžete mobilní aplikace publikovat přímo do obchodů s aplikacemi. Až bude vaše aplikace připravená k stažení uživateli, můžete své binární soubory aplikace publikovat přímo z portálu Visual Studio App Center. 

Můžete přímo distribuovat do:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Obchod Google Play](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
V App Storu, který vyvinul a udržuje společnost Apple, můžou uživatelé procházet a stahovat aplikace vyvinuté pro zařízení s iOS, MacOS, WatchOS a tvOS. Vývojáři potřebují odeslat své aplikace pro iOS do Apple App Storu pro veřejné použití.

### <a name="google-play"></a>Google Play

Google Play je oficiálním obchodem s aplikacemi pro Android, kde můžou uživatelé procházet a stahovat aplikace vyvinuté pro zařízení s Androidem, která jsou publikovaná přes Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) je cloudová služba v prostoru pro správu mobility podnikových zařízení, která pomáhá zajistit produktivitu vašich zaměstnanců a zároveň zajišťuje ochranu podnikových dat. Intune vám umožňuje:
- Spravujte mobilní zařízení a počítače, které vaši zaměstnanci používají pro přístup k firemním datům.
- Spravujte mobilní aplikace, které vaše zaměstnanci používají.
- Chraňte své firemní informace tím, že nastavíte způsob, jakým vaši zaměstnanci přistupují a sdílí je.
- Ujistěte se, že zařízení a aplikace splňují požadavky na zabezpečení společnosti.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Nasazení aktualizací přímo do zařízení uživatelů

### <a name="codepush"></a>CodePush
[CodePush](/appcenter/distribution/codepush/) v App Center Apache Cordova a reagují na nativní vývojáře mohou nasazovat aktualizace mobilních aplikací přímo do svých uživatelských zařízení. Funguje jako centrální úložiště, ve kterém můžou vývojáři publikovat určité aktualizace, například JavaScript, HTML, CSS a změny obrázků. Aplikace se pak mohou dotazovat na aktualizace z úložiště pomocí poskytovaných klientských sad SDK. Tímto způsobem můžete s uživateli pružnější model zapojení a současně vyřešit chyby nebo přidat malé funkce. Nemusíte znovu sestavit binární soubor ani ho znovu distribuovat prostřednictvím jakýchkoli veřejných obchodů s aplikacemi.

**Klíčové funkce**
- Cordova a reagující nativní vývojáře můžou nasazovat aktualizace mobilních aplikací přímo do svých uživatelských zařízení, aniž by se museli uvolnit na obchod.
- Hodí se pro opravy chyb nebo přidávání a odebírání malých funkcí, které nevyžadují opětovné sestavení binárních souborů a jejich distribuci prostřednictvím příslušných obchodů.

**Odkazy**
- [Zaregistrujte se Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s CodePush v App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)
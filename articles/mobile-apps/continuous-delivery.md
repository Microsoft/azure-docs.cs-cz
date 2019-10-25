---
title: Automatizace nasazení a vydání vašich mobilních aplikací pomocí Visual Studio App Center a služeb Azure
description: Přečtěte si o službách, jako je App Center, které vám pomůžou nastavit kanál průběžného doručování pro vaše mobilní aplikace.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795598"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizace nasazení a vydání vašich mobilních aplikací pomocí služeb průběžného doručování

Jako vývojáři napíšete kód a vrátíte se do úložiště kódu, ale potvrzení vrácená do úložiště nemusí být vždy konzistentní. U více vývojářů pracujících na stejném projektu, problémy s integrací a tým by mohl běžet do situací, kdy věci nefungují, chyby se udržují piling a vývoj projektů se bude zpozdit. Vývojáři musí počkat, než se sestaví a otestuje celý softwarový kód, aby se zkontrolovaly chyby, a proces bude pomalý a méně iterativní.

Díky **průběžnému doručování**automatizujete nasazení a vydání vašich mobilních aplikací bez ohledu na to, jestli distribuujete aplikaci do skupiny testerů nebo zaměstnanců společnosti (pro testování beta verze) nebo App Storu (pro produkci). Díky tomu je nasazení méně rizikové, podporuje rychlé iterace a umožňuje nepřetržitě vydávat nové změny pro vaše zákazníky.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuce binárních souborů aplikace do testerů beta verzí
Testování beta verze vaší mobilní aplikace je jedním z důležitých kroků během procesu vývoje aplikace. Pomáhá najít chyby a problémy v aplikaci včas a zpětnou vazbu, která vylepšuje kvalitu vaší aplikace při jejich přípravě na použití v produkčním prostředí.

Pomocí následujících služeb můžete v mobilních aplikacích povolit kanál průběžného doručování.

### <a name="visual-studio-app-center"></a>Centrum aplikací Visual Studio
[App Center Distributed](/appcenter/distribution/) je nástroj, který vývojářům umožňuje rychle vydávat buildy do zařízení koncových uživatelů. S kompletním prostředím instalačního portálu distribuuje distribuce nejen výkonné řešení pro distribuci testů beta aplikací, ale také vhodnou alternativu k distribuci prostřednictvím veřejných obchodů s aplikacemi. Vývojáři můžou svůj pracovní postup distribuce ještě dál automatizovat pomocí App Center integrace s buildem a veřejným úložištěm aplikací.

**Klíčové funkce**
- **Distribuujte své aplikace testerům beta verzí a uživatelům** a ujistěte se, že jsou všichni testeri na nejnovější verzi vaší aplikace.
- **Upozorněte testery na nové verze** bez testerů, kteří procházejí přes tok stahování.
- **Spravujte distribuční skupiny** pro různé verze vaší aplikace.
- **Distribuce do úložišť** 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **Podpora platforem** – iOS, Android, MacOS, TvOS, Xamarin, reaguje na nativní, Unity, Cordova.
- Automaticky zaregistruje zařízení s iOS do vašeho zřizovacího profilu.

**Odkazy**
- [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s distribucí App Center](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) je plně funkční služba pro průběžnou integraci (CI) a průběžné doručování (CD), která spolupracuje s vaším preferovaným poskytovatelem Gitu a kterou můžete nasadit do většiny hlavních cloudových služeb, včetně služeb Azure. Můžete začít s vaším kódem na webu GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud nebo Azure Repos. Potom můžete automatizovat sestavení, testování a nasazení kódu pro Microsoft Azure, Google Cloud Platform nebo Amazon Web Services.

**Klíčové funkce**
- Zjednodušené prostředí založené na úlohách pro nastavení serveru CI pro **nativní mobilní aplikace (Android, iOS a Windows) i pro různé platformy (Xamarin, iOS a reakci na nativní)** .
- **Jakýkoli jazyk, platformu a Cloud** – sestavení, testování a nasazení aplikací Node. js, Python, Java, php, Ruby, přejít, C/C++, C#, Android a iOS. Využívejte souběžné spouštění v Linuxu, macOS a Windows. Nasaďte je do cloudových poskytovatelů, jako je Azure, AWS a GCP. Distribuujte mobilní aplikace přes beta kanály a obchody s aplikacemi.
- **Podpora nativního kontejneru** – snadno vytvářejte nové kontejnery a nahrajte je do libovolného registru. Nasaďte kontejnery na nezávislé hostitele nebo Kubernetes.
- **Pokročilé pracovní postupy a funkce** – jednoduché řetězování sestavení a vícenásobná sestavení. Podpora YAML, integrace testů, bran vydaných verzí, vytváření sestav a dalších.
- **Rozšiřitelné** – použijte řadu úloh sestavení, testování a nasazení vytvořených komunitou – stovky rozšíření od časové rezervy až po SonarCloud. Můžete dokonce nasadit z jiných systémů CI, jako je Jenkinse. Webhooky a rozhraní REST API vám pomůžou integrovat
- **Bezplatné buildy hostované v cloudu** pro veřejná a soukromá úložiště.
- **Podporuje nasazení na jiné dodavatele cloudu** , jako je AWS, GCP atd.

**Odkazy**
- [Začínáme s průvodcem pro Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Začínáme s Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuce aplikace přímo do obchodů s aplikacemi
Jakmile je vaše aplikace připravená na použití v produkčním prostředí a chcete ji použít veřejně, je nutné ji odeslat do obchodů s aplikacemi, kde je mohou stahovat zákazníci. Existuje několik způsobů, jak distribuovat aplikaci přímo do obchodů s aplikacemi. 

### <a name="visual-studio-app-center"></a>Centrum aplikací Visual Studio
Služba [App Center Distributing](/appcenter/distribution/stores/) Service umožňuje publikovat mobilní aplikace přímo do obchodů s aplikacemi. Jakmile bude vaše aplikace připravena ke stažení koncovými uživateli, můžete své binární soubory aplikace publikovat přímo z portálu App Center.  

Můžete přímo distribuovat do:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Obchod Google Play](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
App Store vyvinutý a spravovaný společností Apple umožňuje uživatelům procházet a stahovat aplikace vyvinuté pro zařízení s iOS, MacOS, WatchOS a tvOS. Vývojáři potřebují odeslat své aplikace pro iOS do Apple Storu pro veřejné použití.

### <a name="google-play"></a>Google Play

Google Play je oficiálním obchodem s aplikacemi pro Android, který umožňuje uživatelům procházet a stahovat aplikace vyvinuté pro zařízení s Androidem a publikovat prostřednictvím Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) je cloudová služba v prostoru pro správu mobility podnikových zařízení (EMM), která pomáhá zajistit produktivitu vašich zaměstnanců a zároveň zajistit ochranu firemních dat. S Intune můžete 
- Spravujte mobilní zařízení a počítače, které vaši zaměstnanci používají pro přístup k firemním datům.
- Spravujte mobilní aplikace, které vaše zaměstnanci používají.
- Chraňte své firemní informace tím, že nastavíte způsob, jakým vaši zaměstnanci přistupují a sdílí je.
- Ujistěte se, že zařízení a aplikace vyhovují požadavkům na zabezpečení společnosti.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Nasazení aktualizací přímo do zařízení uživatelů

### <a name="codepush"></a>CodePush
Služba [CodePush](/appcenter/distribution/codepush/) v App Center umožňuje Apache Cordova a reagovat na nativní vývojáře, aby nasadila aktualizace mobilních aplikací přímo do svých uživatelských zařízení. Funguje tak, že funguje jako centrální úložiště, do kterého můžou vývojáři publikovat určité aktualizace (například JS, HTML, CSS a image). Aplikace se pak mohou dotazovat na aktualizace z úložiště pomocí poskytovaných klientských sad SDK. Díky tomu budete mít koncovým uživatelům více deterministické a přímé modelu zapojení, při řešení chyb nebo přidávání malých funkcí, aniž byste museli znovu sestavit binární soubor nebo ho znovu distribuovat prostřednictvím jakýchkoli veřejných obchodů s aplikacemi.

**Klíčové funkce**
- Umožňuje Cordova a reagovat na nativní vývojáře, aby se aktualizace mobilních aplikací nasadily přímo do zařízení uživatelů bez nutnosti vydání na obchod.
- Hodí se pro opravy chyb nebo přidávání nebo odebírání malých funkcí, které nevyžadují opětovné sestavení binárních a rozmístění v příslušných úložištích.

**Odkazy**
- [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s vložením kódu do App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)
---
title: Vytvoření externího pomocného mechanismu
description: Naučte se, jak vytvořit prostředí App Service pomocí aplikace nebo vytvořit samostatný (prázdný) pomocný objekt pro vytváření.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c953c31792b8d01199d409cbd91124138a6ebb15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92927443"
---
# <a name="create-an-external-app-service-environment"></a>Vytvoření externího prostředí App Service

Azure App Service Environment je nasazení služby Azure App Service do podsítě ve virtuální síti Azure.

> [!NOTE]
> Každý App Service Environment má virtuální IP adresu (VIP), která se dá použít ke kontaktování App Service Environment.

Služba App Service Environment (ASE) se dá nasadit dvěma způsoby:

- Pomocí virtuální IP adresy na externí IP adresu – často se označuje jako externí služba ASE
- S VIP na interní IP adrese, která se často označuje jako interního nástroje pomocného mechanismu, protože interní koncový bod je interní Load Balancer (interního nástroje).

V tomto článku se dozvíte, jak vytvořit externí pomocný objekt pro vytváření. Přehled pomocného mechanismu služby najdete v [úvodu k App Service Environment][Intro]. Informace o tom, jak vytvořit interního nástroje pomocného mechanismu pro vytváření, najdete v tématu [Vytvoření a použití POmocného mechanismu interního nástroje][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Před vytvořením pomocného mechanismu

Po vytvoření pomocného mechanismu služby nemůžete změnit následující:

- Umístění
- Předplatné
- Skupina prostředků
- Použitá virtuální síť
- Použitá podsíť
- Velikost podsítě

> [!NOTE]
> Když zvolíte virtuální síť a zadáte podsíť, ujistěte se, že je dostatečně velká, aby vyhovovala budoucím požadavkům na růst a škálování. Doporučujeme velikost `/24` s 256 adresami.
>

## <a name="three-ways-to-create-an-ase"></a>Tři způsoby vytvoření pomocného mechanismu

Existují tři způsoby, jak vytvořit pomocného mechanismu řízení:

- **Při vytváření plánu App Service**. Tato metoda vytvoří pomocného průvodce a plán App Service v jednom kroku.
- **Jako samostatnou akci**. Tato metoda vytvoří samostatný objekt pro vytváření, což je pomocným mechanismem, který v něm není nic. Tato metoda je pokročilejším procesem vytvoření pomocného mechanismu. Použijete ho k vytvoření pomocného objektu s interního nástroje.
- **Ze šablony Azure Resource Manager**. Tato metoda je určena pro pokročilé uživatele. Další informace najdete v tématu [Vytvoření POmocného mechanismu ze šablony][MakeASEfromTemplate].

Externí přístupový modul pro čtení má veřejnou virtuální IP adresu, což znamená, že všechny přenosy HTTP/HTTPS do aplikací v pomocném mechanismu přístupu narazí na internetovou IP adresu. Služba pomocného objektu s interního nástroje má IP adresu z podsítě, kterou používá pomocným mechanismem řízení. Aplikace hostované v pomocném mechanismu interního nástroje se nezveřejňují přímo na internetu.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Vytvoření společně s pomocným mechanismem a App Servicem plánem

Plán App Service je kontejner aplikací. Když vytváříte aplikaci v App Service, zvolíte nebo vytvoříte plán App Service. App Service prostředí uchovávají App Service plány a App Service plány uchovávají aplikace.

Vytvoření pomocného mechanismu pro vytváření App Serviceho plánu:

1. V [Azure Portal](https://portal.azure.com/)vyberte **vytvořit prostředek**  >  **web a mobilní zařízení**  >  **webovou aplikaci**.

    ![Snímek obrazovky Azure Portal zobrazující Web a mobilní zařízení vybrané v Azure Marketplace a na obrazovce pro vytvoření nové webové aplikace na pravé straně][1]

2. Vyberte své předplatné. Aplikace a pomocného mechanismu se vytvoří ve stejných předplatných.

3. Vyberte nebo vytvořte skupinu prostředků. Pomocí skupin prostředků můžete spravovat související prostředky Azure jako jednotku. Skupiny prostředků jsou také užitečné při vytváření Role-Basedch pravidel Access Control pro vaše aplikace. Další informace najdete v tématu [Přehled Azure Resource Manageru][ARMOverview].

4. Vyberte svůj operační systém (Windows, Linux nebo Docker). 

5. Vyberte plán App Service a pak vyberte **vytvořit novou**. Webové aplikace a webové aplikace pro Linux nemůžou být ve stejném plánu App Service, ale můžou být ve stejném App Service Environment. 

    ![Snímek obrazovky Azure Portal zobrazující podokno webové aplikace, podokno plán služby App Service a nové podokno plánu App Service otevřené.][2]

6. V rozevíracím seznamu **umístění** vyberte oblast, ve které chcete vytvořit pomocného panelu. Když vyberete existující pomocným mechanismem, nevytvoří se nový. V rámci pomocného mechanismu, který jste vybrali, se vytvoří plán App Service. 

7. Vyberte **cenovou úroveň** a zvolte jednu z **izolovaných** SKU s cenami. Pokud zvolíte kartu **izolované** SKU a umístění, které není pomocným mechanismem, vytvoří se v tomto umístění nový pomocný objekt pro vytváření. Pokud chcete zahájit proces vytváření pomocného mechanismu, vyberte **Vybrat**. **Izolovaná** SKU je k dispozici pouze ve spojení s pomocným mechanismem řízení. Nemůžete také použít žádnou jinou cenovou jednotku v pomocném objektu, který je jiný než **izolovaný**. 

    ![Výběr cenové úrovně][3]

8. Zadejte název pro pomocného správce. Tento název se používá v adresních názvech pro vaše aplikace. Pokud je název pomocného mechanismu _appsvcenvdemo_, název domény je *. appsvcenvdemo.p.azurewebsites.NET*. Pokud vytvoříte aplikaci s názvem *MyTestApp*, tato adresa se dá adresovat na MyTestApp.appsvcenvdemo.p.azurewebsites.NET. V názvu nelze použít prázdné znaky. Pokud použijete velká písmena, název domény je celková verze tohoto názvu.

    ![Název nového plánu App Service][4]

9. Zadejte podrobnosti o virtuální síti Azure. Vyberte možnost **vytvořit novou** nebo **Vyberte existující**. Možnost výběru existující virtuální sítě je dostupná jenom v případě, že ve vybrané oblasti máte virtuální síť. Pokud vyberete **vytvořit nový**, zadejte název virtuální sítě. Vytvoří se nová virtuální síť Správce prostředků s tímto názvem. Používá adresní prostor `192.168.250.0/23` ve vybrané oblasti. Pokud vyberete **Vybrat existující**, budete potřebovat:

    a. Vyberte blok adres virtuální sítě, pokud máte více než jeden.

    b. Zadejte nový název podsítě.

    c. Vyberte velikost podsítě. *Nezapomeňte si vybrat dostatečně velkou velikost, aby vyhovovala budoucímu růstu vašeho pomocného mechanismu.* Doporučujeme, abyste měli `/24` k dis256 adresám a mohli zpracovat maximální velikost POmocného mechanismu. Nedoporučujeme `/28` například, protože k dispozici jsou jenom 16 adres. Infrastruktura používá minimálně sedm adres a síť Azure používá další 5. V `/28` podsíti zůstanete s maximálním počtem 4 App Service instancí plánu pro externí POmocného mechanismu pro zápis a jenom 3 App Service plánování instancí pro interního nástroje.

    d. Vyberte rozsah IP adres podsítě.

10. Vyberte **vytvořit** a vytvořte tak pomocného mechanismu. Tento proces také vytvoří plán App Service a aplikaci. Pomocného nástroje, App Service plán a aplikace jsou všechny v rámci stejného předplatného a také ve stejné skupině prostředků. Pokud vaše pomocné služby potřebuje samostatnou skupinu prostředků, nebo pokud potřebujete interního nástroje pomocného mechanismu, postupujte podle pokynů pro vytvoření mechanismu řízení.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Vytvořte si pomocného mechanismu pro čtení a webovou aplikaci pro Linux s využitím vlastní image Docker.

1. V [Azure Portal](https://portal.azure.com/)vytvořte web a mobilní zařízení **prostředku**  >    >  **Web App for Containers.** 

    ![Snímek obrazovky Azure Portal zobrazující Web a mobilní zařízení vybrané v Azure Marketplace a podokno Web App for Containers otevřené vpravo.][7]

1. Vyberte své předplatné. Aplikace a pomocného mechanismu se vytvoří ve stejných předplatných.

1. Vyberte nebo vytvořte skupinu prostředků. Pomocí skupin prostředků můžete spravovat související prostředky Azure jako jednotku. Skupiny prostředků jsou také užitečné při vytváření Role-Basedch pravidel Access Control pro vaše aplikace. Další informace najdete v tématu [Přehled Azure Resource Manageru][ARMOverview].

1. Vyberte plán App Service a pak vyberte **vytvořit novou**. Webové aplikace a webové aplikace pro Linux nemůžou být ve stejném plánu App Service, ale můžou být ve stejném App Service Environment. 

    ![Snímek obrazovky Azure Portal znázorňující podokno Web App for Containers, podokno plán služby App Service a nové podokno App Service plán se otevřelo.][8]

1. V rozevíracím seznamu **umístění** vyberte oblast, ve které chcete vytvořit pomocného panelu. Když vyberete existující pomocným mechanismem, nevytvoří se nový. V rámci pomocného mechanismu, který jste vybrali, se vytvoří plán App Service. 

1. Vyberte **cenovou úroveň** a zvolte jednu z **izolovaných** SKU s cenami. Pokud zvolíte kartu **izolované** SKU a umístění, které není pomocným mechanismem, vytvoří se v tomto umístění nový pomocný objekt pro vytváření. Pokud chcete zahájit proces vytváření pomocného mechanismu, vyberte **Vybrat**. **Izolovaná** SKU je k dispozici pouze ve spojení s pomocným mechanismem řízení. Nemůžete také použít žádnou jinou cenovou jednotku v pomocném objektu, který je jiný než **izolovaný**. 

    ![Výběr cenové úrovně][3]

1. Zadejte název pro pomocného správce. Tento název se používá v adresních názvech pro vaše aplikace. Pokud je název pomocného mechanismu _appsvcenvdemo_, název domény je *. appsvcenvdemo.p.azurewebsites.NET*. Pokud vytvoříte aplikaci s názvem *MyTestApp*, tato adresa se dá adresovat na MyTestApp.appsvcenvdemo.p.azurewebsites.NET. V názvu nelze použít prázdné znaky. Pokud použijete velká písmena, název domény je celková verze tohoto názvu.

    ![Název nového plánu App Service][4]

1. Zadejte podrobnosti o virtuální síti Azure. Vyberte možnost **vytvořit novou** nebo **Vyberte existující**. Možnost výběru existující virtuální sítě je dostupná jenom v případě, že ve vybrané oblasti máte virtuální síť. Pokud vyberete **vytvořit nový**, zadejte název virtuální sítě. Vytvoří se nová virtuální síť Správce prostředků s tímto názvem. Používá adresní prostor `192.168.250.0/23` ve vybrané oblasti. Pokud vyberete **Vybrat existující**, budete potřebovat:

    a. Vyberte blok adres virtuální sítě, pokud máte více než jeden.

    b. Zadejte nový název podsítě.

    c. Vyberte velikost podsítě. *Nezapomeňte si vybrat dostatečně velkou velikost, aby vyhovovala budoucímu růstu vašeho pomocného mechanismu.* Doporučujeme, abyste měli `/24` k dis128 adresám a mohli zpracovat maximální velikost POmocného mechanismu. Nedoporučujeme `/28` například, protože k dispozici jsou jenom 16 adres. Infrastruktura používá minimálně sedm adres a síť Azure používá další 5. V `/28` podsíti zůstanete s maximálním počtem 4 App Service instancí plánu pro externí POmocného mechanismu pro zápis a jenom 3 App Service plánování instancí pro interního nástroje.

    d. Vyberte rozsah IP adres podsítě.

1.  Vyberte konfigurovat kontejner.
    * Zadejte název vlastní image (můžete použít Azure Container Registry, Docker Hub a vlastní privátní registr). Pokud nechcete používat svůj vlastní kontejner, můžete ho jednoduše přenést a použít integrovanou image s App Service v systému Linux pomocí výše uvedených pokynů. 

    ![Konfigurovat kontejner][9]

1. Vyberte **vytvořit** a vytvořte tak pomocného mechanismu. Tento proces také vytvoří plán App Service a aplikaci. Pomocného nástroje, App Service plán a aplikace jsou všechny v rámci stejného předplatného a také ve stejné skupině prostředků. Pokud vaše pomocné služby potřebuje samostatnou skupinu prostředků, nebo pokud potřebujete interního nástroje pomocného mechanismu, postupujte podle pokynů pro vytvoření mechanismu řízení.


## <a name="create-an-ase-by-itself"></a>Vytvoření namocného mechanismu služby samostatně

Pokud vytvoříte samostatného pomocného mechanismu řízení, nemá nic v něm. Prázdnému pomocnému mechanismu řízení se pořád účtuje měsíční poplatek za infrastrukturu. Postupujte podle těchto kroků a vytvořte pomocí služby interního nástroje nebo vytvořte v vlastní skupině prostředků pomocného mechanismu řízení. Po vytvoření pomocného mechanismu služby můžete v něm vytvářet aplikace pomocí normálního procesu. Jako umístění vyberte nový pomocného panelu.

1. Vyhledejte Azure Marketplace pro **App Service Environment** nebo vyberte **vytvořit prostředek**  >  **Web mobilní**  >  **App Service Environment**. 

1. Zadejte název vašeho pomocného programu. Tento název se používá pro aplikace vytvořené v pomocném formuláři. Pokud je název *mynewdemoase*, název subdomény je *. mynewdemoase.p.azurewebsites.NET*. Pokud vytvoříte aplikaci s názvem *MyTestApp*, tato adresa se dá adresovat na MyTestApp.mynewdemoase.p.azurewebsites.NET. V názvu nelze použít prázdné znaky. Použijete-li velká písmena, název domény je celková verze názvu. Pokud použijete interního nástroje, váš název pomocného mechanismu se v subdoméně nepoužije, ale místo toho se zadává explicitně během vytváření pomocného mechanismu.

    ![Pojmenování pomocného mechanismu][5]

1. Vyberte své předplatné. Toto předplatné je zároveň ta, kterou používají všechny aplikace v pomocném formuláři. Do virtuální sítě, která je v jiném předplatném, nemůžete dát svůj příhlasu.

1. Vyberte nebo zadejte novou skupinu prostředků. Skupina prostředků použitá pro pomocného správce musí být stejná jako ta, která se používá ve vaší virtuální síti. Když vyberete existující virtuální síť, aktualizuje se výběr skupiny prostředků pro pomocného mechanismu, aby odrážela vaši virtuální síť. *Pokud použijete šablonu Správce prostředků, můžete vytvořit pomocné služby se skupinou prostředků, která se liší od skupiny prostředků virtuální sítě.* Informace o vytvoření služby pomocného mechanismu ze šablony najdete v tématu [vytvoření App Serviceho prostředí ze šablony][MakeASEfromTemplate].

    ![Výběr skupiny prostředků][6]

1. Vyberte svou virtuální síť a umístění. Můžete vytvořit novou virtuální síť nebo vybrat existující virtuální síť: 

    * Pokud vyberete novou virtuální síť, můžete zadat její název a umístění. 
    
    * Nová virtuální síť má rozsah adres 192.168.250.0/23 a podsíť s názvem default. Podsíť je definovaná jako 192.168.250.0/24. Můžete vybrat jenom Správce prostředků virtuální síť. Výběr **typu VIP** určuje, jestli k vašemu přimocnému objektu se dá získat přímý pøístup z Internetu (externí) nebo jestli používá interního nástroje. Další informace o těchto možnostech najdete v tématu [Vytvoření a použití interního nástroje pro vyrovnávání zatížení v prostředí App Service][MakeILBASE]. 

      * Pokud pro **Typ VIP** vyberete **externí** , můžete vybrat, kolik externích IP adres se má systém vytvořit, pro účely protokolu SSL založeného na protokolu IP. 
    
      * Pokud pro **Typ VIP** vyberete **interní** , musíte zadat doménu, kterou používá váš správce přidaných mechanismů. Pomocného mechanismu můžete nasadit do virtuální sítě, která používá veřejné nebo soukromé rozsahy adres. Chcete-li použít virtuální síť s rozsahem veřejných adres, je nutné vytvořit virtuální síť předem. 
    
    * Pokud vyberete existující virtuální síť, vytvoří se při vytvoření pomocného mechanismu řízení Nová podsíť. *V portálu nemůžete použít předem vytvořenou podsíť. Pokud použijete šablonu Správce prostředků, můžete vytvořit pomocného programu s existující podsítí.* Informace o vytvoření pomocného mechanismu ze šablony naleznete v tématu [vytvoření App Service Environment ze šablony][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service Environment v1

Stále můžete vytvářet instance první verze App Service Environment (ASEv1). Pokud chcete tento proces spustit, vyhledejte na webu Marketplace **App Service Environment v1**. Pomocného mechanismu pro vytváření můžete vytvořit stejným způsobem jako samostatný pomocným mechanismem pro vytváření. Až se dokončí, vaše ASEv1 má dvě front-endy a dva pracovní procesy. V ASEv1 musíte spravovat front-endy a pracovníky. Při vytváření plánů App Service nejsou automaticky přidány. Front-endy slouží jako koncové body HTTP/HTTPS a odesílají do pracovních procesů provoz. Pracovní procesy jsou role hostující vaše aplikace. Po vytvoření pomocného mechanismu služeb můžete upravit množství front-endy a pracovníků. 

Další informace o ASEv1 najdete v tématu [Úvod do App Service Environment v1][ASEv1Intro]. Další informace o škálování, správě a monitorování ASEv1 naleznete v tématu [How to Configure a App Service Environment][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md

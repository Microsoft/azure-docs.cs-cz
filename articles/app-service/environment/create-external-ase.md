---
title: Vytvoření externí ase
description: Zjistěte, jak vytvořit prostředí služby App Service s aplikací v něm, nebo vytvořit samostatnou (prázdnou) službu ASE.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6c4838e3226b91cbb5d6f86b83266a986418c120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430510"
---
# <a name="create-an-external-app-service-environment"></a>Vytvoření prostředí externí služby App Service

Azure App Service Environment je nasazení služby Azure App Service do podsítě ve virtuální síti Azure.

> [!NOTE]
> Každé prostředí služby App Service má virtuální IP (VIP), kterou lze použít ke kontaktování prostředí služby App Service.

Služba App Service Environment (ASE) se dá nasadit dvěma způsoby:

- Pomocí virtuální IP adresy na externí IP adresu – často se označuje jako externí služba ASE
- S VIP na interní IP adresu, často volal ASE ILB, protože vnitřní koncový bod je vnitřní vyrovnávání zatížení (ILB).

Tento článek ukazuje, jak vytvořit externí ase. Přehled služby ASE najdete [v tématu úvod do prostředí služby App Service][Intro]. Informace o vytvoření služby ASE ilb naleznete v [tématu Vytvoření a použití služby ASE služby ILB][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Před vytvořením ase

Po vytvoření ase nelze změnit následující:

- Umístění
- Předplatné
- Skupina prostředků
- Použitá virtuální síť
- Použitá podsíť
- Velikost podsítě

> [!NOTE]
> Když zvolíte virtuální síť a zadáte podsíť, ujistěte se, že je dostatečně velká, aby vyhovovala budoucím potřebám růstu a škálování. Doporučujeme velikost `/24` s 256 adresami.
>

## <a name="three-ways-to-create-an-ase"></a>Tři způsoby vytvoření ase

Ase lze vytvořit třemi způsoby:

- **Při vytváření plánu služby App Service**. Tato metoda vytvoří službu ASE a plán služby App Service v jednom kroku.
- **Jako samostatná akce**. Tato metoda vytvoří samostatnou ase, což je ase s ničím v něm. Tato metoda je pokročilejší proces k vytvoření ase. Slouží k vytvoření služby ASE s ILB.
- **Ze šablony Správce prostředků Azure**. Tato metoda je určen pro pokročilé uživatele. Další informace naleznete [v tématu Vytvoření ase ze šablony][MakeASEfromTemplate].

Externí služba ASE má veřejnou virtuální IP adresu, což znamená, že veškerý přenos http/https do aplikací v ase služby ASE narazí na IP adresu přístupnou k internetu. ASE s ILB má IP adresu z podsítě používané službou ASE. Aplikace hostované ve službě ASE ILB nejsou vystaveny přímo internetu.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Vytvoření služby ASE a plánu služby App Service společně

Plán služby App Service je kontejner aplikací. Když vytvoříte aplikaci ve službě App Service, zvolíte nebo vytvoříte plán služby App Service. Prostředí služby App Service mají plány služby App Service a plány služby App Service.

Vytvoření služby ASE při vytváření plánu služby App Service:

1. Na [portálu Azure](https://portal.azure.com/)vyberte **Vytvořit** > web **+ mobilní** > **webovou aplikaci .**

    ![Vytvoření webové aplikace][1]

2. Vyberte své předplatné. Aplikace a služby ASE jsou vytvořeny ve stejných předplatných.

3. Vyberte nebo vytvořte skupinu prostředků. Se skupinami prostředků můžete spravovat související prostředky Azure jako jednotku. Skupiny prostředků jsou také užitečné při vytváření pravidel řízení přístupu na základě rolí pro vaše aplikace. Další informace najdete v tématu [Přehled Azure Resource Manageru][ARMOverview].

4. Vyberte operační systém (Windows, Linux nebo Docker). 

5. Vyberte plán Služby App Service a pak vyberte **Vytvořit nový**. Linuxové webové aplikace a webové aplikace pro Windows nemohou být ve stejném plánu služby App Service, ale mohou být ve stejném prostředí služby App Service. 

    ![Nový plán služby App Service][2]

6. V rozevíracím seznamu **Umístění** vyberte oblast, do které chcete službu ASE vytvořit. Pokud vyberete existující ase, nová ase není vytvořen. Plán služby App Service se vytvoří ve službě ASE, kterou jste vybrali. 

7. Vyberte **Cenovou úroveň**a zvolte jednu z **izolovaných** cenových slok. Pokud zvolíte **samostatnou** kartu sku a umístění, které není službou ASE, vytvoří se v tomto umístění nová zpráva služby ASE. Chcete-li zahájit proces vytvoření ase, vyberte **vybrat**. Izolované skladové **položky** je k dispozici pouze ve spojení se službou ASE. Nelze také použít žádné jiné ceny Skladové položky v ase jiné než **izolované**. 

    ![Výběr cenové úrovně][3]

8. Zadejte název své ase. Tento název se používá v adresovatelném názvu vašich aplikací. Pokud je název služby ASE _appsvcenvdemo_, název domény je *.appsvcenvdemo.p.azurewebsites.net*. Pokud vytvoříte aplikaci s názvem *mytestapp*, je adresovatelná na mytestapp.appsvcenvdemo.p.azurewebsites.net. V názvu nelze použít prázdné znaky. Pokud používáte velká písmena, název domény je celková verze s velkými písmeny tohoto názvu.

    ![Název plánu Nové služby App Service][4]

9. Zadejte podrobnosti o virtuální síti Azure. Vyberte **možnost Vytvořit nový** nebo Vybrat **existující**. Možnost vybrat existující virtuální síť je k dispozici jenom v případě, že máte virtuální síť ve vybrané oblasti. Pokud vyberete **Vytvořit nový**, zadejte název virtuální sítě. Vytvoří se nová virtuální síť Správce prostředků s tímto názvem. Používá adresní `192.168.250.0/23` prostor ve vybrané oblasti. Pokud vyberete **Vybrat existující**, musíte:

    a. Pokud máte víc než jeden blok adresy virtuální sítě, vyberte blok adresy virtuální sítě.

    b. Zadejte nový název podsítě.

    c. Vyberte velikost podsítě. *Nezapomeňte vybrat dostatečně velkou velikost, aby vyhovovala budoucímu růstu vaší ase.* Doporučujeme `/24`, který má 128 adres a může zpracovávat maximální velikosti ase. Nedoporučujeme `/28`například , protože je k dispozici pouze 16 adres. Infrastruktura používá alespoň sedm adres a Azure Networking používá dalších 5. V `/28` podsíti zůstane maximální škálování 4 instance plánu služby App Service pro externí službu ASE a pouze 3 instance plánu služby App Service pro službu ASE ILB.

    d. Vyberte rozsah IP podsítě.

10. Chcete-li vytvořit ase, vyberte **vytvořit.** Tento proces také vytvoří plán služby App Service a aplikace. Služba ASE, plán služby App Service a aplikace jsou všechny pod stejným předplatným a také ve stejné skupině prostředků. Pokud vaše ase potřebuje samostatnou skupinu prostředků nebo pokud potřebujete službu ASE ILB, postupujte podle pokynů k vytvoření služby ASE samostatně.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Vytvoření služby ASE a webové aplikace pro Linux pomocí vlastní image Dockeru společně

1. Na [portálu Azure](https://portal.azure.com/) **vytvořte** > web prostředků **+ mobilní** > **webovou aplikaci pro kontejnery.** 

    ![Vytvoření webové aplikace][7]

1. Vyberte své předplatné. Aplikace a služby ASE jsou vytvořeny ve stejných předplatných.

1. Vyberte nebo vytvořte skupinu prostředků. Se skupinami prostředků můžete spravovat související prostředky Azure jako jednotku. Skupiny prostředků jsou také užitečné při vytváření pravidel řízení přístupu na základě rolí pro vaše aplikace. Další informace najdete v tématu [Přehled Azure Resource Manageru][ARMOverview].

1. Vyberte plán Služby App Service a pak vyberte **Vytvořit nový**. Linuxové webové aplikace a webové aplikace pro Windows nemohou být ve stejném plánu služby App Service, ale mohou být ve stejném prostředí služby App Service. 

    ![Nový plán služby App Service][8]

1. V rozevíracím seznamu **Umístění** vyberte oblast, do které chcete službu ASE vytvořit. Pokud vyberete existující ase, nová ase není vytvořen. Plán služby App Service se vytvoří ve službě ASE, kterou jste vybrali. 

1. Vyberte **Cenovou úroveň**a zvolte jednu z **izolovaných** cenových slok. Pokud zvolíte **samostatnou** kartu sku a umístění, které není službou ASE, vytvoří se v tomto umístění nová zpráva služby ASE. Chcete-li zahájit proces vytvoření ase, vyberte **vybrat**. Izolované skladové **položky** je k dispozici pouze ve spojení se službou ASE. Nelze také použít žádné jiné ceny Skladové položky v ase jiné než **izolované**. 

    ![Výběr cenové úrovně][3]

1. Zadejte název své ase. Tento název se používá v adresovatelném názvu vašich aplikací. Pokud je název služby ASE _appsvcenvdemo_, název domény je *.appsvcenvdemo.p.azurewebsites.net*. Pokud vytvoříte aplikaci s názvem *mytestapp*, je adresovatelná na mytestapp.appsvcenvdemo.p.azurewebsites.net. V názvu nelze použít prázdné znaky. Pokud používáte velká písmena, název domény je celková verze s velkými písmeny tohoto názvu.

    ![Název plánu Nové služby App Service][4]

1. Zadejte podrobnosti o virtuální síti Azure. Vyberte **možnost Vytvořit nový** nebo Vybrat **existující**. Možnost vybrat existující virtuální síť je k dispozici jenom v případě, že máte virtuální síť ve vybrané oblasti. Pokud vyberete **Vytvořit nový**, zadejte název virtuální sítě. Vytvoří se nová virtuální síť Správce prostředků s tímto názvem. Používá adresní `192.168.250.0/23` prostor ve vybrané oblasti. Pokud vyberete **Vybrat existující**, musíte:

    a. Pokud máte víc než jeden blok adresy virtuální sítě, vyberte blok adresy virtuální sítě.

    b. Zadejte nový název podsítě.

    c. Vyberte velikost podsítě. *Nezapomeňte vybrat dostatečně velkou velikost, aby vyhovovala budoucímu růstu vaší ase.* Doporučujeme `/24`, který má 128 adres a může zpracovávat maximální velikosti ase. Nedoporučujeme `/28`například , protože je k dispozici pouze 16 adres. Infrastruktura používá alespoň sedm adres a Azure Networking používá dalších 5. V `/28` podsíti zůstane maximální škálování 4 instance plánu služby App Service pro externí službu ASE a pouze 3 instance plánu služby App Service pro službu ASE ILB.

    d. Vyberte rozsah IP podsítě.

1.  Vyberte možnost Konfigurovat kontejner.
    * Zadejte svůj vlastní název bitové kopie (můžete použít Azure Container Registry, Docker Hub a vlastní soukromý registr). Pokud nechcete používat vlastní kontejner, stačí přenést svůj kód a použít integrovanou bitovou kopii se službou App Service na Linuxu pomocí výše uvedených pokynů. 

    ![Konfigurace kontejneru][9]

1. Chcete-li vytvořit ase, vyberte **vytvořit.** Tento proces také vytvoří plán služby App Service a aplikace. Služba ASE, plán služby App Service a aplikace jsou všechny pod stejným předplatným a také ve stejné skupině prostředků. Pokud vaše ase potřebuje samostatnou skupinu prostředků nebo pokud potřebujete službu ASE ILB, postupujte podle pokynů k vytvoření služby ASE samostatně.


## <a name="create-an-ase-by-itself"></a>Vytvoření ase samostatně

Pokud vytvoříte samostatný soubor ASE, nemá nic v něm. Prázdné ase stále účtuje měsíční poplatek za infrastrukturu. Podle těchto kroků vytvořte službu ASE s ILB nebo vytvořte službu ASE ve vlastní skupině prostředků. Po vytvoření služby ASE můžete vytvářet aplikace v ní pomocí normálního procesu. Jako umístění vyberte novou službu ASE.

1. Prohledejte prostředí Azure Marketplace for **App Service Nebo**vyberte **Vytvořit** > prostředí**služby Web Mobile** > **App Service .** 

1. Zadejte název své ase. Tento název se používá pro aplikace vytvořené v ase. Pokud je název *mynewdemoase*, název subdomény je *.mynewdemoase.p.azurewebsites.net*. Pokud vytvoříte aplikaci s názvem *mytestapp*, je adresovatelná na mytestapp.mynewdemoase.p.azurewebsites.net. V názvu nelze použít prázdné znaky. Pokud používáte velká písmena, název domény je celková verze s ložených písmenem názvu. Pokud používáte ILB, název služby ASE se nepoužívá ve vaší subdoméně, ale je místo toho explicitně uvedeno během vytváření služby ASE.

    ![Pojmenování ase][5]

1. Vyberte své předplatné. Toto předplatné je také ten, který používají všechny aplikace ve službě ASE. Nelze umístit službu ASE do virtuální sítě, která je v jiném předplatném.

1. Vyberte nebo zadejte novou skupinu prostředků. Skupina prostředků používaná pro vaši sestavu se musí samých, která se používá pro vaši virtuální síť. Pokud vyberete existující virtuální síť, výběr skupiny prostředků pro vaši ase se aktualizuje tak, aby odrážela výběr vaší virtuální sítě. *Pokud používáte šablonu Správce prostředků, můžete vytvořit službu ASE se skupinou prostředků, která se liší od skupiny prostředků virtuální sítě.* Pokud chcete vytvořit službu ASE ze šablony, přečtěte si informace [o vytvoření prostředí služby App Service ze šablony][MakeASEfromTemplate].

    ![Výběr skupiny prostředků][6]

1. Vyberte virtuální síť a umístění. Můžete vytvořit novou virtuální síť nebo vybrat existující virtuální síť: 

    * Pokud vyberete novou virtuální síť, můžete zadat její název a umístění. 
    
    * Nová virtuální síť má rozsah adres 192.168.250.0/23 a podsíť s názvem výchozí. Podsíť je definována jako 192.168.250.0/24. Můžete vybrat jenom virtuální síť Správce prostředků. Výběr **typu VIP** určuje, zda je vaše ase přímo přístupná z internetu (externí) nebo zda používá ILB. Další informace o těchto možnostech najdete v [tématu Vytvoření a použití interního nástroje pro vyrovnávání zatížení v prostředí služby App Service][MakeILBASE]. 

      * Pokud pro typ **VIP**vyberete **externí** , můžete vybrat, kolik externích IP adres je systém vytvořen pro účely protokolu SSL založené na protokolu IP. 
    
      * Pokud pro typ **VIP**vyberete **interní** , musíte zadat doménu, kterou vaše služba ASE používá. Službu ASE můžete nasadit do virtuální sítě, která používá rozsahy veřejných nebo privátní chodů adres. Pokud chcete použít virtuální síť s rozsahem veřejných adres, musíte virtuální síť vytvořit předem. 
    
    * Pokud vyberete existující virtuální síť, vytvoří se při vytvoření ase nová podsíť. *Na portálu nelze použít předem vytvořenou podsíť. Pokud používáte šablonu Správce prostředků, můžete vytvořit službu ASE s existující podsítí.* Pokud chcete vytvořit službu ASE ze šablony, přečtěte si [informace o vytvoření prostředí služby App Service z šablony][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service Environment v1

Stále můžete vytvořit instance první verze prostředí služby App Service (ASEv1). Chcete-li tento proces spustit, vyhledejte na webu Marketplace pro **prostředí služby App Service v1**. Vytvoření ase stejným způsobem, který vytvoříte samostatné ase. Po dokončení má asev1 dva přední konce a dva pracovníky. S ASEv1, musíte spravovat front-endy a pracovníky. Při vytváření plánů služby App Service se nepřidávají automaticky. Front-endy fungují jako koncové body HTTP/HTTPS a odesílají provoz pracovníkům. Pracovníci jsou role, které hostují vaše aplikace. Po vytvoření fronty můžete upravit množství front-endů a pracovníků. 

Další informace o ASEv1 najdete [v tématu Úvod do prostředí služby App Service v1][ASEv1Intro]. Další informace o škálování, správu a monitorování ASEv1 naleznete v [tématu Jak nakonfigurovat prostředí služby App Service][ConfigureASEv1].

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
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md

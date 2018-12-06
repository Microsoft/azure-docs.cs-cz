---
title: Vytvoření externí Azure App Service environment
description: Vysvětluje, jak vytvořit prostředí služby App Service při vytváření aplikace nebo samostatné
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 6df708c97750c89c850c993d0e1a43ded01934a2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959957"
---
# <a name="create-an-external-app-service-environment"></a>Vytvoření externí App Service environment #

Azure App Service Environment je nasazení služby Azure App Service do podsítě ve virtuální síti Azure. Služba App Service Environment (ASE) se dá nasadit dvěma způsoby:

- Pomocí virtuální IP adresy na externí IP adresu – často se označuje jako externí služba ASE
- Pomocí virtuální IP adresy na interní IP adresa často se označuje ILB ASE protože interním koncovým bodem je interní nástroj pro vyrovnávání zatížení (ILB).

Tento článek ukazuje, jak vytvořit externí služby ASE. Přehled služby ASE najdete v tématu [Úvod do služby App Service Environment][Intro]. Informace o tom, jak vytvořit prostředí ILB ASE najdete v tématu [vytvoření a použití prostředí ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Před vytvořením služby ASE ##

Po vytvoření služby ASE, nelze změnit následující:

- Umístění
- Předplatné
- Skupina prostředků
- Použít virtuální síť
- Podsíť používá
- Velikost podsítě

> [!NOTE]
> Vyberete virtuální síť a zadejte podsíť, ujistěte se, že je dostatečně velký pro přizpůsobení budoucímu růstu a potřeby škálování. Doporučujeme velikost `/24` s 256 adres.
>

## <a name="three-ways-to-create-an-ase"></a>Tři způsoby, jak vytvořit službu ASE ##

Existují tři způsoby, jak vytvořit službu ASE:

- **Při vytváření plánu služby App Service**. Tato metoda vytvoří službu ASE a plán služby App Service v jednom kroku.
- **Jako samostatnou akci**. Tato metoda vytvoří samostatnou službu ASE, což je služba ASE s žádné v ní. Tato metoda je pokročilejší proces vytvoření služby ASE. Použijete ji k vytvoření služby ASE s ILB.
- **Ze šablony Azure Resource Manageru**. Tato metoda je pro zkušené uživatele. Další informace najdete v tématu [vytvoření ASE ze šablony][MakeASEfromTemplate].

Externí služba ASE má veřejnou virtuální IP adresy, což znamená, že všechny přenosy HTTP/HTTPS k aplikacím ve službě ASE narazí na IP adresu přístupné z Internetu. Služba ASE s ILB má IP adresu z podsítě používané služby ASE. Aplikace hostované ve službě ASE s ILB nejsou přímo zveřejněné Internetu.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Vytvoření služby ASE a plán služby App Service společně ##

Plán služby App Service je kontejner aplikace. Při vytváření aplikace ve službě App Service, zvolte nebo vytvořte plán služby App Service. Služby App Service Environment podržte plány služby App Service a plány služby App Service uchování aplikace.

Vytvoření služby ASE, při vytváření plánu služby App Service:

1. V [webu Azure portal](https://portal.azure.com/)vyberte **vytvořit prostředek** > **Web + mobilní zařízení** > **webovou aplikaci**.

    ![Vytvoření webové aplikace][1]

1. Vyberte své předplatné. Aplikace a služby ASE se vytvoří ve stejném předplatném.

1. Vyberte nebo vytvořte skupinu prostředků. Se skupinami prostředků můžete spravovat související prostředky Azure jako celek. Skupiny prostředků také jsou užitečné, když vytvořit pravidla řízení přístupu na základě rolí pro vaše aplikace. Další informace najdete v tématu [přehled Azure Resource Manageru][ARMOverview].

1. Vyberte svůj operační systém (Windows, Linuxu a Dockeru). 

1. Vyberte plán služby App Service a pak vyberte **vytvořit nový**. Linuxové webové aplikace a webové aplikace Windows nemůžou být ve stejném plánu služby App Service, ale může být ve stejné službě App Service Environment. 

    ![Nový plán služby App Service][2]

1. V **umístění** rozevíracího seznamu vyberte oblast, ve kterém chcete vytvořit službu ASE. Pokud vyberete stávající služby ASE, nové služby ASE se nevytvoří. Plán služby App Service se vytvoří ve službě ASE, kterou jste vybrali. 

1. Vyberte **cenová úroveň**a zvolte jednu z **izolované** ceny skladových položek. Pokud se rozhodnete **izolované** SKU karty a umístění, které není služba ASE nové služby ASE se vytvoří v příslušném umístění. Chcete-li zahájit proces vytvoření služby ASE, vyberte **vyberte**. **Izolované** SKU je k dispozici pouze ve spojení s ASE. Také nelze použít jiné cenové skladová položka ve službě ASE jiné než **izolované**. 

    ![Výběr cenové úrovně][3]

1. Zadejte název vaší služby ase. Tento název se používá v názvu adresovatelný pro vaše aplikace. Pokud je název služby ASE _appsvcenvdemo_, název domény je *. appsvcenvdemo.p.azurewebsites.net*. Pokud vytvoříte aplikaci s názvem *mytestapp*, je na mytestapp.appsvcenvdemo.p.azurewebsites.net adresovatelný. Prázdné znaky nelze použít v názvu. Pokud budete používat velká písmena, název domény je celkový počet malá verze s tímto názvem.

    ![Nový název plánu služby App Service][4]

1. Zadejte vaše Azure podrobnosti virtuální sítě. Vyberte buď **vytvořit nový** nebo **vybrat existující**. Možnost vybrat stávající virtuální síť je dostupná jenom v případě, že máte virtuální síť ve vybrané oblasti. Pokud vyberete **vytvořit nový**, zadejte název pro virtuální síť. Vytvoří se nová virtuální síť Resource Manageru s tímto názvem. Používá adresní prostor `192.168.250.0/23` ve vybrané oblasti. Pokud vyberete **vybrat existující**, budete muset:

    a. Blok adres virtuální sítě, vyberte, pokud máte více než jeden.

    b. Zadejte název nové podsítě.

    c. Vyberte velikost podsítě. *Nezapomeňte vybrat velikost dostatečně velký pro přizpůsobení budoucímu růstu vaší služby ase.* Doporučujeme `/25`, která nabízí 128 adres a dokáže zpracovat maximální velikost služby ASE. Nedoporučujeme ale `/28`, například, protože nejsou k dispozici pouze 16 adres. Infrastruktura používá minimálně sedm adres a sítě Azure používá jiný 5. V `/28` podsítě, se připojíme maximálně pouze 3 a 4 instancí plánu služby App Service pro externí služby ASE škálování instance plánu služby App Service pro službu ASE.

    d. Vyberte rozsah IP adres podsítě.

1. Vyberte **vytvořit** k vytvoření služby ASE. Tento proces také vytvoří plán služby App Service a aplikaci. Služby ASE, plán služby App Service a aplikace jsou všechno ve stejném předplatném a také ve stejné skupině prostředků. Pokud vaše služba ASE potřebuje do samostatné skupiny prostředků nebo pokud potřebujete službu ASE, postupujte podle kroků pro vytvoření služby ASE samostatně.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Vytvoření služby ASE a Linuxovou webovou aplikaci pomocí vlastní image Dockeru dohromady

1. V [webu Azure portal](https://portal.azure.com/), **vytvořit prostředek** > **Web + mobilní zařízení** > **Web App for Containers.** 

    ![Vytvoření webové aplikace][7]

1. Vyberte své předplatné. Aplikace a služby ASE se vytvoří ve stejném předplatném.

1. Vyberte nebo vytvořte skupinu prostředků. Se skupinami prostředků můžete spravovat související prostředky Azure jako celek. Skupiny prostředků také jsou užitečné, když vytvořit pravidla řízení přístupu na základě rolí pro vaše aplikace. Další informace najdete v tématu [přehled Azure Resource Manageru][ARMOverview].

1. Vyberte plán služby App Service a pak vyberte **vytvořit nový**. Linuxové webové aplikace a webové aplikace Windows nemůžou být ve stejném plánu služby App Service, ale může být ve stejné službě App Service Environment. 

    ![Nový plán služby App Service][8]

1. V **umístění** rozevíracího seznamu vyberte oblast, ve kterém chcete vytvořit službu ASE. Pokud vyberete stávající služby ASE, nové služby ASE se nevytvoří. Plán služby App Service se vytvoří ve službě ASE, kterou jste vybrali. 

1. Vyberte **cenová úroveň**a zvolte jednu z **izolované** ceny skladových položek. Pokud se rozhodnete **izolované** SKU karty a umístění, které není služba ASE nové služby ASE se vytvoří v příslušném umístění. Chcete-li zahájit proces vytvoření služby ASE, vyberte **vyberte**. **Izolované** SKU je k dispozici pouze ve spojení s ASE. Také nelze použít jiné cenové skladová položka ve službě ASE jiné než **izolované**. 

    ![Výběr cenové úrovně][3]

1. Zadejte název vaší služby ase. Tento název se používá v názvu adresovatelný pro vaše aplikace. Pokud je název služby ASE _appsvcenvdemo_, název domény je *. appsvcenvdemo.p.azurewebsites.net*. Pokud vytvoříte aplikaci s názvem *mytestapp*, je na mytestapp.appsvcenvdemo.p.azurewebsites.net adresovatelný. Prázdné znaky nelze použít v názvu. Pokud budete používat velká písmena, název domény je celkový počet malá verze s tímto názvem.

    ![Nový název plánu služby App Service][4]

1. Zadejte vaše Azure podrobnosti virtuální sítě. Vyberte buď **vytvořit nový** nebo **vybrat existující**. Možnost vybrat stávající virtuální síť je dostupná jenom v případě, že máte virtuální síť ve vybrané oblasti. Pokud vyberete **vytvořit nový**, zadejte název pro virtuální síť. Vytvoří se nová virtuální síť Resource Manageru s tímto názvem. Používá adresní prostor `192.168.250.0/23` ve vybrané oblasti. Pokud vyberete **vybrat existující**, budete muset:

    a. Blok adres virtuální sítě, vyberte, pokud máte více než jeden.

    b. Zadejte název nové podsítě.

    c. Vyberte velikost podsítě. *Nezapomeňte vybrat velikost dostatečně velký pro přizpůsobení budoucímu růstu vaší služby ase.* Doporučujeme `/25`, která nabízí 128 adres a dokáže zpracovat maximální velikost služby ASE. Nedoporučujeme ale `/28`, například, protože nejsou k dispozici pouze 16 adres. Infrastruktura používá minimálně sedm adres a sítě Azure používá jiný 5. V `/28` podsítě, se připojíme maximálně pouze 3 a 4 instancí plánu služby App Service pro externí služby ASE škálování instance plánu služby App Service pro službu ASE.

    d. Vyberte rozsah IP adres podsítě.

1.  Vyberte "Konfigurace kontejneru."
    * Zadejte název vlastní image (můžete použít Azure Container Registry a Docker Hubu, privátního registru). Pokud nechcete použít vlastní kontejner, vám stačí dodejte vlastní kód a integrované image pomocí služby App Service v Linuxu pomocí výše uvedených pokynů. 

    ![Konfigurace kontejneru][9]

1. Vyberte **vytvořit** k vytvoření služby ASE. Tento proces také vytvoří plán služby App Service a aplikaci. Služby ASE, plán služby App Service a aplikace jsou všechno ve stejném předplatném a také ve stejné skupině prostředků. Pokud vaše služba ASE potřebuje do samostatné skupiny prostředků nebo pokud potřebujete službu ASE, postupujte podle kroků pro vytvoření služby ASE samostatně.


## <a name="create-an-ase-by-itself"></a>Vytvoření prostředí ASE sám o sobě ##

Pokud vytvoříte samostatné služby ASE, nemá žádné v ní. Prázdný služby ASE se stále účtují měsíční poplatky za infrastrukturu. Postupujte podle těchto kroků k vytvoření služby ASE s ILB, nebo vytvořit službu ASE ve vlastní skupině prostředků. Po vytvoření služby ASE se v něm můžete vytvořit aplikace s použitím normální proces. Jako umístění vyberte nové služby ASE.

1. Na Azure Marketplace vyhledejte **služby App Service Environment**, nebo vyberte **vytvořit prostředek** > **mobilní Web** > **aplikace Služba prostředí**. 

1. Zadejte název vaší služby ASE. Tento název se používá pro aplikace vytvořené ve službě ASE. Pokud je název *mynewdemoase*, je název subdomény *. mynewdemoase.p.azurewebsites.net*. Pokud vytvoříte aplikaci s názvem *mytestapp*, je na mytestapp.mynewdemoase.p.azurewebsites.net adresovatelný. Prázdné znaky nelze použít v názvu. Pokud budete používat velká písmena, název domény je celkový počet malá verzí názvu. Pokud používáte ILB, název vaší služby ASE se nepoužívá v vaše subdomény, ale místo toho výslovně stanovena během vytváření služby ASE.

    ![Služba ASE pojmenování][5]

1. Vyberte své předplatné. Toto předplatné je také všechny aplikace ve službě ASE používat. Nelze vložit svoji službu ASE ve virtuální síti, která je v jiném předplatném.

1. Vyberte nebo zadejte novou skupinu prostředků. Skupina prostředků použitá pro vaši službu ASE musí být stejný jako ten, který se používá pro vaši virtuální síť. Pokud vyberete stávající virtuální síť, výběr skupiny prostředků pro vaši službu ASE se aktualizuje tak, aby odrážely, který vaší virtuální sítě. *Můžete vytvořit službu ASE s skupinu prostředků, která se liší od skupiny prostředků virtuální sítě, pokud použijete šablonu Resource Manageru.* Vytvoření ASE ze šablony najdete v tématu [vytvoření služby App Service environment pomocí šablony][MakeASEfromTemplate].

    ![Výběr skupiny prostředků][6]

1. Vyberte virtuální síť a umístění. Můžete vytvořit novou virtuální síť nebo vybrat stávající virtuální síť: 

    * Pokud vyberete novou virtuální síť, můžete zadat její název a umístění. 
    
    * Nové virtuální sítě má 192.168.250.0/23 rozsah adres a podsíť s názvem výchozí. Podsíť je definován jako 192.168.250.0/24. Můžete vybrat pouze virtuální síť Resource Manageru. **Typ VIP** výběr určuje, zda vaše služba ASE je přímo přístupný z Internetu (externí), nebo pokud používá ILB. Další informace o těchto možnostech najdete v tématu [vytvoření a použití interního nástroje load balancer pomocí služby App Service environment][MakeILBASE]. 

      * Pokud vyberete **externí** pro **typ VIP**, můžete vybrat kolik externích IP adres v systému se vytvoří s pro účely založené na protokolu IP SSL. 
    
      * Pokud vyberete **interní** pro **typ VIP**, je nutné zadat doménu, která používá vaše služba ASE. Můžete nasadit službu ASE do virtuální sítě, které se používají rozsahy adres veřejné nebo soukromé. Chcete-li používat virtuální síť s veřejnou adresu rozsahu, vytvořte virtuální síť předem. 
    
    * Pokud vyberete stávající virtuální síť, novou podsíť se vytvoří při vytvoření služby ASE. *Nelze použít předem vytvořený podsítě na portálu. Pokud použijete šablonu Resource Manageru, můžete vytvořit službu ASE s existující podsítí.* Vytvoření ASE ze šablony najdete v tématu [vytvoření služby App Service Environment pomocí šablony][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

Přesto vytvořit instance první verzi služby App Service Environment (ASEv1). Chcete-li spustit tento proces, na Marketplace vyhledejte **služby App Service Environment v1**. Vytvoření služby ASE stejným způsobem, který vytvoříte samostatné služby ASE. Po dokončení, vaše ASEv1 obsahuje dva front-endů a dva pracovní procesy. ASEv1 musí spravovat front-endů a pracovních procesů. Přidávají se automaticky při vytváření vašich plánech služby App Service. Front-endů fungovat jako koncové body HTTP/HTTPS a odesílat provoz do zaměstnanců. Zaměstnanci jsou role, které jsou hostiteli vaše aplikace. Počet front-endů a pracovních procesů můžete upravit po vytvoření služby ASE. 

Další informace o verzi ASEv1 najdete v tématu [Úvod do služby App Service Environment v1][ASEv1Intro]. Další informace o škálování, správu a sledování ASEv1, naleznete v tématu [konfigurace služby App Service Environment][ConfigureASEv1].

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
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md

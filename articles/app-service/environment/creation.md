---
title: Vytvoření App Service Environment
description: Naučte se vytvářet App Service Environment.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a0d474208f11c203ca65e9ac296fa381d8633a8b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663567"
---
# <a name="create-an-app-service-environment"></a>Vytvoření App Service Environment

> [!NOTE]
> Tento článek se týká App Service Environment V3 (Preview).
> 

[App Service Environment (POmocným mechanismem)][Intro] je nasazení App Service jednoho tenanta, které se vloží do Azure Virtual Network (VNET).  ASEv3 podporuje zpřístupnění aplikací jenom na privátní adrese ve vaší virtuální síti. Při vytvoření ASEv3 ve verzi Preview se do vašeho předplatného přidá tři prostředky.

- App Service Environment
- Azure DNS privátní zóna
- Privátní koncový bod

Nasazení služby pomocného mechanismu bude vyžadovat použití dvou podsítí.  Jedna podsíť bude obsahovat privátní koncový bod.  Tuto podsíť je možné použít pro jiné věci, jako jsou virtuální počítače.  Druhá podsíť se používá pro odchozí volání prováděná z pomocného mechanismu.  Tuto podsíť nelze použít pro jiné jiné než pomocného mechanismu. 

## <a name="before-you-create-your-ase"></a>Před vytvořením pomocného mechanismu

Po vytvoření pomocného mechanismu služby se nedá změnit:

- Umístění
- Předplatné
- Skupina prostředků
- Používá se Azure Virtual Network (VNet).
- Používané podsítě
- Velikost podsítě
- Název vašeho pomocného programu

Odchozí podsíť musí být dostatečně velká, aby mohla být uložena maximální velikost, kterou budete škálovat k pomocnému mechanismu řízení. Vyberte si dostatečnou podsíť, která bude podporovat vaše maximální požadavky na škálování, protože po vytvoření se nedá změnit. Doporučená velikost je/24 s adresou 256.

Po vytvoření pomocného mechanismu řízení můžete do něj přidávat aplikace. Pokud v ASEv3 neexistují žádné plány App Service, účtuje se vám i v případě, že jste do svého pomocného programu pro pořízení služby I1v2 naplánovali jednu instanci App Service.  

ASEv3 je nabídnuta pouze ve vybraných oblastech. Další oblasti budou přidány v rámci verze Preview směrem k GA. 

## <a name="creating-an-ase-in-the-portal"></a>Vytvoření pomocného mechanismu pro vytváření na portálu

1. Pokud chcete vytvořit ASEv3, vyhledejte na webu Marketplace **App Service Environment V3**.  
2. Základy: vyberte předplatné, vyberte nebo vytvořte skupinu prostředků a zadejte název vašeho přidaných mechanismů.  Název pomocného programu se použije i pro příponu vašeho přidaného správce domény.  Pokud je název pomocného mechanismu služby *Contoso* , bude přípona domény *contoso.appserviceenvironment.NET*.  Tento název se automaticky nastaví v Azure DNS privátní zóně, kterou používá virtuální síť, do které se nasadí pomocný modul pro nasazování. 

    ![Karta základy vytváření App Service Environment](./media/creation/creation-basics.png)

3. Hostování: vyberte Předvolby operačního systému, nasazení skupiny hostitelů. Předvolba operačního systému indikuje, že operační systém, který poprvé použijete pro vaše aplikace v tomto pomocném mechanismu. Po vytvoření pomocného programu můžete přidat aplikace druhého operačního systému. K výběru vyhrazeného hardwaru se používá nasazení skupiny hostitelů. Pomocí ASEv3 můžete vybrat povoleno a pak se na vyhrazený hardware doložit. Účtují se vám poplatky za celého vyhrazeného hostitele s vytvářením pomocného programu a pak sníženou cenu za instance App Service plánu. 

    ![App Service Environment hostování výběrů](./media/creation/creation-hosting.png)

4. Sítě: vyberte nebo vytvořte Virtual Network, vyberte nebo vytvořte svou příchozí podsíť, vyberte nebo vytvořte svou odchozí podsíť. Všechny podsítě používané pro odchozí připojení musí být prázdné a musí být delegovány na Microsoft. Web/hostingEnvironments. Pokud vytvoříte podsíť přes portál, bude podsíť automaticky delegována.

    ![Výběry sítě App Service Environment](./media/creation/creation-networking.png)

5. Zkontrolovat a vytvořit: Zkontrolujte správnost konfigurace a vyberte vytvořit. Vytvoření pomocného mechanismu bude trvat přibližně hodinu. 

    ![App Service Environment zkontrolovat a vytvořit](./media/creation/creation-review.png)

Po dokončení vytváření pomocného mechanismu můžete vybrat ho jako umístění při vytváření aplikací. Další informace o vytváření aplikací v novém pomocném objektu pro čtení najdete v tématu [použití App Service Environment][UsingASE]

## <a name="os-preference"></a>Předvolba operačního systému
V pomocném mechanismu služby můžete mít aplikace pro Windows, aplikace pro Linux nebo obojí. V ASEv2 počáteční preference vybraná při vytváření přidá k tomuto operačnímu systému během vytváření pomocného mechanismu infrastrukturu vysoké dostupnosti. Pokud chcete přidat aplikace v jiném operačním systému, stačí nastavit aplikace jako obvykle a vybrat operační systém, který chcete. V ASEv3 to nebude mít vliv na chování back-endu appreciatively.  

## <a name="dedicated-hosts"></a>Vyhrazení hostitelé
Pomocného nástroje se obvykle nasazuje na virtuálních počítačích, které jsou zřízené na hypervisoru s více klienty. Pokud potřebujete nasadit na vyhrazené systémy, včetně hardwaru, můžete zřídit svůj pomocný přístup na vyhrazené hostitele. V počátečním ASEv3 ve verzi Preview přicházejí vyhrazení hostitelé do páru. Každý vyhrazený hostitel je v samostatné zóně dostupnosti, pokud ho tato oblast povoluje. Vyhradovaná nasazení pomocí přihlášeného hostitele se účtují jinak než normálně. U vyhrazeného hostitele se účtuje poplatek a pak se za každou instanci App Service plánu doúčtuje další poplatek.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md

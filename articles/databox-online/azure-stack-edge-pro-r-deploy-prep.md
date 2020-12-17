---
title: Kurz pro přípravu Azure Portal prostředí Datacenter k nasazení Azure Stack Edge pro R
description: První kurz o nasazení Azure Stack Edge pro R zahrnuje přípravu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 7ca9b21838d35b54b4ed84d5aaf3aa797b02d9e0
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630764"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-r"></a>Kurz: Příprava na nasazení Azure Stack Edge pro R

Toto je první kurz v sérii kurzů pro nasazení, které jsou nutné k úplnému nasazení Azure Stack Edge pro R. V tomto kurzu se dozvíte, jak připravit Azure Portal k nasazení Azure Stack hraničního prostředku. V tomto kurzu se používá Azure Stack hraničního zařízení pro R, které se dodává s nepřerušitelným zdrojem napájení (UPS).

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

### <a name="get-started"></a>Začínáme

Pokud chcete nasadit Azure Stack Edge pro R, přečtěte si následující kurzy v předepsané posloupnosti.

| K provedení tohoto kroku | Použijte tyto dokumenty |
| --- | --- |
| **Součást** |Tyto kroky je potřeba provést v přípravě na nadcházející nasazení. |
| **[Kontrolní seznam konfigurace nasazení](#deployment-configuration-checklist)** |Kontrolní seznam použijte ke shromáždění a zaznamenání informací před nasazením a během nasazení. |
| **[Požadavky nasazení](#prerequisites)** |Splnění požadavků nasazení kvalifikuje prostředí jako připravené na nasazení. |
|  | |
|**Kurzy k nasazení** |Tyto kurzy se vyžadují k nasazení zařízení Azure Stack Edge pro R v produkčním prostředí. |
|**[1. Příprava Azure Portal pro zařízení](azure-stack-edge-pro-r-deploy-prep.md)** |Před instalací fyzického zařízení hraničního Azure Stack box vytvořte a nakonfigurujte Azure Stack hraničního prostředku. |
|**[2. Nainstalujte zařízení.](azure-stack-edge-pro-r-deploy-install.md)**|Zkontrolujte a kabelujte fyzické zařízení.  |
|**[3. Připojte se k zařízení.](azure-stack-edge-pro-r-deploy-connect.md)** |Jakmile je zařízení nainstalované, připojte se k místnímu webovému uživatelskému rozhraní zařízení.  |
|**[4. Konfigurace nastavení sítě](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)** |Nakonfigurujte síť včetně nastavení výpočetní sítě a webového proxy serveru pro vaše zařízení.   |
|**[5. Konfigurace nastavení zařízení](azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)** |Přiřaďte název zařízení a doménu DNS, nakonfigurujte server aktualizací a čas zařízení. |
|**[6. Konfigurace nastavení zabezpečení](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)** |Konfigurace certifikátů, sítě VPN, šifrování v klidovém prostředí pro vaše zařízení. Použijte certifikáty vygenerované zařízením nebo využijte vlastní certifikáty.   |
|**[7. aktivace zařízení](azure-stack-edge-pro-r-deploy-activate.md)** |K aktivaci zařízení použijte aktivační klíč ze služby. Zařízení je připravené nastavovat sdílené složky SMB nebo NFS nebo je připojit prostřednictvím REST. |
|**[8. konfigurace COMPUTE](azure-stack-edge-gpu-deploy-configure-compute.md)** |Nakonfigurujte na svém zařízení výpočetní roli. Tím se také vytvoří cluster Kubernetes. |

Teď můžete začít nastavovat Azure Portal.

## <a name="deployment-configuration-checklist"></a>Kontrolní seznam konfigurace nasazení

Před nasazením zařízení je potřeba shromáždit informace ke konfiguraci softwaru na zařízení Azure Stack Edge pro. Když se některé z těchto informací předem připravují, pomůže vám to zjednodušit proces nasazení zařízení ve vašem prostředí. Pomocí [kontrolního seznamu konfigurace nasazení Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-checklist.md) si poznamenejte podrobnosti konfigurace při nasazování zařízení.

## <a name="prerequisites"></a>Požadavky

Níže jsou uvedené požadavky na konfiguraci pro váš Azure Stack hraniční zdroj, vaše zařízení Azure Stack Edge a síť datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Pro prostředek Azure Stack Edge

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Pro zařízení Azure Stack Edge

Před nasazením fyzického zařízení se ujistěte, že:

- Prozkoumali jste bezpečnostní informace pro toto zařízení v: [pokyny pro zabezpečení Azure Stack hraničního zařízení](azure-stack-edge-pro-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 



### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

- Síť v datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Azure Stack Edge. Další informace najdete v tématu [požadavky na systém Azure Stack Edge pro R](azure-stack-edge-pro-r-system-requirements.md).

- Pro normální provozní podmínky vašeho zařízení máte následující:

    - Minimální šířka pásma ke stažení, která zajistí, že se zařízení bude aktualizovat, musí mít minimálně 10 MB/s.
    - Minimálně 20 MB/s vyhrazeným nahráváním a šířkou pásma pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud máte existující Azure Stack hraniční prostředek pro správu fyzického zařízení, přeskočte tento krok a přejděte na [získat aktivační klíč](#get-the-activation-key).

Chcete-li vytvořit prostředek Azure Stack Edge, proveďte v Azure Portal následující kroky.

1. Pomocí přihlašovacích údajů pro Microsoft Azure se přihlaste k Azure Portal na této adrese URL: [https://portal.azure.com](https://portal.azure.com) .

2. V levém podokně vyberte **+ vytvořit prostředek**. Vyhledejte a vyberte **Azure Stack Edge/data box Gateway**. Vyberte **Vytvořit**. 

3. Vyberte předplatné, které chcete použít pro zařízení Azure Stack Edge pro. Vyberte zemi, do které chcete toto fyzické zařízení dodávat. Vyberte **Zobrazit zařízení**.

    ![Vytvoření prostředku 1](media/azure-stack-edge-pro-r-deploy-prep/create-resource-1.png)

4. Vyberte typ zařízení. V části **Azure Stack Edge** zvolte **Azure Stack Edge pro R** a pak zvolte **Vybrat**. Pokud se zobrazí nějaké problémy nebo nemůžete vybrat typ zařízení, přejděte k tématu [řešení potíží s pořadím](azure-stack-edge-troubleshoot-ordering.md).

    ![Vytvoření prostředku 2](media/azure-stack-edge-pro-r-deploy-prep/create-resource-2.png)

5. Podle potřeby vaší firmy můžete vybrat **Azure Stack jeden uzel Edge pro r** nebo **Azure Stack Edge pro r jeden uzel se zdrojem UPS**.  

    ![Vytvoření prostředku 3](media/azure-stack-edge-pro-r-deploy-prep/create-resource-3.png)

6. Na kartě **základy** zadejte nebo vyberte následující **Podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |Tato hodnota se vyplní automaticky na základě dřívějšího výběru. Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).     |

7. Zadejte nebo vyberte následující **Podrobnosti o instanci**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název   | Popisný název pro identifikaci prostředku.<br>Název musí obsahovat 2 až 50 znaků a může obsahovat písmena, číslice a spojovníky.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Oblast     |Seznam všech oblastí, kde jsou k dispozici prostředky Azure Stack Edge, najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Pokud používáte Azure Government, jsou dostupné všechny oblasti státní správy, jak je znázorněno v [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|

    ![Vytvoření prostředku 4](media/azure-stack-edge-pro-r-deploy-prep/create-resource-4.png)


8. Vyberte **Další: adresa příjemce**.

    - Pokud už máte nějaké zařízení, vyberte pole se seznamem pro **zařízení Azure Stack Edge pro R**.

        ![Vytvoření prostředku 5](media/azure-stack-edge-pro-r-deploy-prep/create-resource-5.png)

    - Pokud se jedná o nové zařízení, které objednáváte, zadejte jméno kontaktu, společnost, adresu pro odeslání zařízení a kontaktní údaje.

        ![Vytvoření prostředku 6](media/azure-stack-edge-pro-r-deploy-prep/create-resource-6.png)

9. Vyberte **Další: značky**. Volitelně můžete zadat značky pro kategorizaci prostředků a konsolidaci fakturace. Vyberte **Další: Zkontrolovat a vytvořit**.

10. Na kartě **Revize + vytvořit** si přečtěte **Podrobnosti o cenách**, **podmínky použití** a podrobnosti k vašemu prostředku. Zaškrtněte pole se seznamem pro **uživatele, kteří si prošli podmínka ochrany osobních údajů**.

    ![Vytvoření prostředku 7](media/azure-stack-edge-pro-r-deploy-prep/create-resource-7.png) 

    Při vytváření prostředku se také zobrazí upozornění, že je povolená Identita spravované služby (MSI), která umožňuje ověření pro cloudové služby. Tato identita existuje, pokud prostředek existuje.

11. Vyberte **Vytvořit**.

Vytvoření prostředku trvá několik minut. Vytvoří se taky soubor MSI, který umožňuje, aby zařízení Azure Stack Edge komunikovalo se zprostředkovatelem prostředků v Azure.

Po úspěšném vytvoření a nasazení prostředku budete upozorněni. Vyberte **Přejít k prostředku**.

![Přejít na prostředek Azure Stack Edge pro](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-1.png)

Po uvedení objednávky Microsoft zkontroluje pořadí a zavede vám (prostřednictvím e-mailu) informace o expedici.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

Pokud narazíte na problémy v průběhu procesu objednávky, přečtěte si téma [řešení potíží s pořadím](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Až bude prostředek Azure Stack Edge v provozu, budete muset získat aktivační klíč. Tento klíč se používá k aktivaci a připojení zařízení Azure Stack Edge pro k prostředku. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte prostředek, který jste vytvořili. Vyberte **Přehled** a pak vyberte **nastavení zařízení**.

    ![Vybrat nastavení zařízení](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-2.png)

2. Na dlaždici **aktivovat** zadejte název Azure Key Vault nebo přijměte výchozí název. Název trezoru klíčů může být dlouhý 3 až 24 znaků. 

    Pro každý Azure Stack hraničního prostředku, který se aktivuje s vaším zařízením, se vytvoří Trezor klíčů. Trezor klíčů umožňuje ukládat a přistupovat k tajným klíčům, například klíč integrity kanálu (CIK) pro službu, je uložený v trezoru klíčů. 

    Jakmile zadáte název trezoru klíčů, vyberte **vygenerovat klíč** a vytvořte aktivační klíč. 

    ![Získání aktivačního klíče](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-3.png)

    Počkejte několik minut, než se vytvoří Trezor klíčů a aktivační klíč. Vyberte ikonu kopírování a zkopírujte klíč a uložte ho pro pozdější použití.


> [!IMPORTANT]
> - Aktivační klíč vyprší tři dny po vygenerování.
> - Pokud vypršela platnost klíče, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s tématy Azure Stack Edge, jako jsou:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Přejděte k dalšímu kurzu, kde se dozvíte, jak nainstalovat Azure Stack Edge.

> [!div class="nextstepaction"]
> [Nainstalovat Azure Stack Edge](./azure-stack-edge-pro-r-deploy-install.md)

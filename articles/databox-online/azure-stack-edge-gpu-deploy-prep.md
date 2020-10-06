---
title: Kurz pro přípravu Azure Portalho prostředí Datacenter k nasazení Azure Stack pro GPU Edge pro | Microsoft Docs
description: První kurz nasazení Azure Stack Edge pro GPU zahrnuje přípravu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 1d207e7cc052af32917eb6c871f332136580e56c
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743252"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>Kurz: Příprava na nasazení Azure Stack Edge pro s grafickým procesorem 

Toto je první kurz v sérii kurzů pro nasazení, které jsou potřeba k úplnému nasazení Azure Stack Edge pro s grafickým procesorem. V tomto kurzu se dozvíte, jak připravit Azure Portal k nasazení Azure Stack hraničního prostředku.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

### <a name="get-started"></a>Začínáme

Pro nasazení Azure Stack Edge pro je nutné nejprve připravit prostředí. Jakmile je prostředí připravené, postupujte podle požadovaných kroků a v případě potřeby proveďte volitelné kroky a postupy pro úplné nasazení zařízení. Podrobné pokyny k nasazení určují, kdy byste měli provést všechny požadované a volitelné kroky.

| Krok | Popis |
| --- | --- |
| **Příprava** |Tyto kroky je potřeba provést v přípravě na nadcházející nasazení. |
| **[Kontrolní seznam konfigurace nasazení](#deployment-configuration-checklist)** |Kontrolní seznam použijte ke shromáždění a zaznamenání informací před nasazením a během nasazení. |
| **[Požadavky nasazení](#prerequisites)** |Splnění požadavků nasazení kvalifikuje prostředí jako připravené na nasazení. |
|  | |
|**Kurzy k nasazení** |Tyto kurzy se vyžadují k nasazení zařízení Azure Stack Edge pro v produkčním prostředí. |
|**[1. Příprava Azure Portal pro Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md)** |Před instalací fyzického zařízení hraničního Azure Stack box vytvořte a nakonfigurujte Azure Stack hraničního prostředku. |
|**[2. instalace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-install.md)**|Rozbalení, stojan a kabel fyzického zařízení Azure Stack Edge pro.  |
|**[3. připojení k Azure Stack Edge pro](azure-stack-edge-gpu-deploy-connect.md)** |Jakmile je zařízení nainstalované, připojte se k místnímu webovému uživatelskému rozhraní zařízení.  |
|**[4. Konfigurace nastavení sítě pro Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Nakonfigurujte síť včetně nastavení výpočetní sítě a webového proxy serveru pro vaše zařízení.   |
|**[5. Konfigurace nastavení zařízení pro Azure Stack Edge pro](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Přiřaďte název zařízení a doménu DNS, nakonfigurujte server aktualizací a čas zařízení. |
|**[6. Konfigurace nastavení zabezpečení pro Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Nakonfigurujte certifikáty pro vaše zařízení. Použijte certifikáty vygenerované zařízením nebo využijte vlastní certifikáty.   |
|**[7. aktivovat Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md)** |K aktivaci zařízení použijte aktivační klíč ze služby. Zařízení je připravené nastavovat sdílené složky SMB nebo NFS nebo je připojit prostřednictvím REST. |
|**[8. konfigurace COMPUTE](azure-stack-edge-gpu-deploy-configure-compute.md)** |Nakonfigurujte na svém zařízení výpočetní roli. Tím se také vytvoří cluster Kubernetes. |
|**[9a. Přenos dat pomocí hraničních sdílení](azure-stack-edge-j-series-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |
|**[9b. Přenos dat pomocí hraničních účtů úložiště](azure-stack-edge-j-series-deploy-add-storage-accounts.md)** |Přidejte účty úložiště a připojte se k úložišti objektů BLOB prostřednictvím rozhraní REST API. |


Teď můžete začít shromažďovat informace týkající se konfigurace softwaru pro zařízení Azure Stack Edge pro.

## <a name="deployment-configuration-checklist"></a>Kontrolní seznam konfigurace nasazení

Před nasazením zařízení je potřeba shromáždit informace ke konfiguraci softwaru na zařízení Azure Stack Edge pro. Když se některé z těchto informací předem připravují, pomůže vám to zjednodušit proces nasazení zařízení ve vašem prostředí. Pomocí [kontrolního seznamu konfigurace nasazení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-checklist.md) si poznamenejte podrobnosti konfigurace při nasazování zařízení.


## <a name="prerequisites"></a>Předpoklady

Níže jsou uvedené požadavky na konfiguraci pro váš Azure Stack hraniční prostředek, vaše zařízení Azure Stack Edge pro a síť datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Pro prostředek Azure Stack Edge

Než začnete, ujistěte se, že:

- Vaše předplatné Microsoft Azure je povolené pro prostředek Azure Stack Edge. Ujistěte se, že jste použili podporované předplatné, jako je například [Microsoft smlouva Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)nebo [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Předplatná s průběžnými platbami se nepodporují. Pokud chcete zjistit, jaký typ předplatného Azure máte, přečtěte si téma [co je nabídka Azure?](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer).
- Máte oprávnění vlastníka nebo přispěvatele na úrovni skupiny prostředků pro Azure Stack hraniční pro/Data Box Gateway, IoT Hub a Azure Storage prostředky.

    - Pokud chcete vytvořit Azure Stack hraničního nebo Data Box Gatewayho prostředku, měli byste mít oprávnění jako Přispěvatel (nebo vyšší) vymezené na úrovni skupiny prostředků. 
    - Také je nutné zajistit, aby `Microsoft.DataBoxEdge` `MicrosoftKeyVault` poskytovatelé prostředků a byl zaregistrován. Pokud chcete vytvořit libovolný prostředek IoT Hub, `Microsoft.Devices` měl by se zaregistrovat poskytovatel. 
        - Pokud chcete zaregistrovat poskytovatele prostředků, v Azure Portal přejít na **domovskou > předplatná > > poskytovatelé prostředků vašich předplatných**. 
        - Vyhledejte konkrétního poskytovatele prostředků, například `Microsoft.DataBoxEdge` a zaregistrujte poskytovatele prostředků. 
    - Pokud chcete vytvořit prostředek účtu úložiště, musíte pro něj znovu použít rozsah přístupu přispěvatele nebo vyšší úrovně. Azure Storage je ve výchozím nastavení registrovaný poskytovatel prostředků.
- Správce nebo uživatel má přístup k Azure Active Directory Graph API pro generování aktivačního klíče nebo operací s přihlašovacími údaji, jako je vytvoření sdílené složky, který používá účet úložiště. Další informace najdete v tématu [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).


### <a name="for-the-azure-stack-edge-pro-device"></a>Pro zařízení Azure Stack Edge pro

Před nasazením fyzického zařízení se ujistěte, že:

- Zkontrolovali jste bezpečnostní údaje, které byly součástí balíčku dodávek.
- Máte slot 1U, který je ve vašem datacentru k dispozici ve standardu 19 "Rack pro připojení zařízení.
- Máte přístup k plochému, stabilnímu a pracovnímu pracovnímu prostoru, kde může zařízení bezpečně klidovat.
- Lokalita, ve které máte v úmyslu zařízení nastavit, má standardní sílu napájení z nezávislého zdroje nebo jednotky PDU (AC) (napájení) s nepřerušitelným zdrojem napájení (UPS).
- Máte přístup k fyzickému zařízení.


### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

- Síť v datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Azure Stack Edge pro. Další informace najdete v tématu [požadavky na systém Azure Stack Edge pro](azure-stack-edge-system-requirements.md).

- Pro normální provozní podmínky vaší Azure Stack Edge pro máte následující:

    - Minimální šířka pásma ke stažení, která zajistí, že se zařízení bude aktualizovat, musí mít minimálně 10 MB/s.
    - Minimálně 20 MB/s vyhrazeným nahráváním a šířkou pásma pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud máte existující Azure Stack hraniční prostředek pro správu fyzického zařízení, přeskočte tento krok a přejděte na [získat aktivační klíč](#get-the-activation-key).

Chcete-li vytvořit prostředek Azure Stack Edge, proveďte v Azure Portal následující kroky.

1. Pomocí přihlašovacích údajů pro Microsoft Azure se přihlaste k Azure Portal na této adrese URL: [https://portal.azure.com](https://portal.azure.com) .

2. V levém podokně vyberte **+ vytvořit prostředek**. Vyhledejte a vyberte **Azure Stack Edge/data box Gateway**. Vyberte **Vytvořit**. 

3. Vyberte předplatné, které chcete použít pro zařízení Azure Stack Edge pro. Vyberte zemi, do které chcete toto fyzické zařízení dodávat. Vyberte **Zobrazit zařízení**.

    ![Vytvoření prostředku 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Vyberte typ zařízení. V části **Azure Stack Edge pro**zvolte **Azure Stack Edge pro s grafickým procesorem** a pak zvolte **Vybrat**. Pokud se zobrazí nějaké problémy nebo nemůžete vybrat typ zařízení, přejděte k tématu [řešení potíží s pořadím](azure-stack-edge-troubleshoot-ordering.md).

    ![Vytvoření prostředku 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Na základě potřeb vaší firmy můžete vybrat Azure Stack Edge pro s 1 nebo 2 grafickými procesory (GPU) z NVIDIA. 

    ![Vytvoření prostředku 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. Na kartě **základy** zadejte nebo vyberte následující **Podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |Tato hodnota se vyplní automaticky na základě dřívějšího výběru. Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/resource-group-overview.md).     |

7. Zadejte nebo vyberte následující **Podrobnosti o instanci**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název   | Popisný název pro identifikaci prostředku.<br>Název musí obsahovat 2 až 50 znaků a může obsahovat písmena, číslice a spojovníky.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Oblast     |Seznam všech oblastí, kde jsou k dispozici prostředky Azure Stack Edge, najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Pokud používáte Azure Government, jsou dostupné všechny oblasti státní správy, jak je znázorněno v [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|

    ![Vytvoření prostředku 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)


8. Vyberte **Další: adresa příjemce**.

    - Pokud už máte nějaké zařízení, vyberte pole se seznamem, **které má zařízení Azure Stack Edge pro**.

        ![Vytvoření prostředku 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Pokud se jedná o nové zařízení, které objednáváte, zadejte jméno kontaktu, společnost, adresu pro odeslání zařízení a kontaktní údaje.

        ![Vytvoření prostředku 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. Vyberte **Další: značky**. Volitelně můžete zadat značky pro kategorizaci prostředků a konsolidaci fakturace. Vyberte **Další: Zkontrolovat a vytvořit**.

10. Na kartě **Revize + vytvořit** si přečtěte **Podrobnosti o cenách**, **podmínky použití**a podrobnosti k vašemu prostředku. Zaškrtněte pole se seznamem pro **uživatele, kteří si prošli podmínka ochrany osobních údajů**.

    ![Vytvoření prostředku 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png) 

    Při vytváření prostředku se také zobrazí upozornění, že je povolená Identita spravované služby (MSI), která umožňuje ověření pro cloudové služby. Tato identita existuje, pokud prostředek existuje.

11. Vyberte **Vytvořit**.

Vytvoření prostředku trvá několik minut. Vytvoří se taky soubor MSI, který umožňuje, aby zařízení Azure Stack Edge komunikovalo se zprostředkovatelem prostředků v Azure.

Po úspěšném vytvoření a nasazení prostředku budete upozorněni. Vyberte **Přejít k prostředku**.

![Přejít na prostředek Azure Stack Edge pro](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

Po uvedení objednávky Microsoft zkontroluje pořadí a zavede vám (prostřednictvím e-mailu) informace o expedici.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

Pokud narazíte na problémy v průběhu procesu objednávky, přečtěte si téma [řešení potíží s pořadím](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Až bude prostředek Azure Stack Edge v provozu, budete muset získat aktivační klíč. Tento klíč se používá k aktivaci a připojení zařízení Azure Stack Edge pro k prostředku. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte prostředek, který jste vytvořili. Vyberte **Přehled** a pak vyberte **nastavení zařízení**.

    ![Vybrat nastavení zařízení](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)

2. Na dlaždici **aktivovat** zadejte název Azure Key Vault nebo přijměte výchozí název. Název trezoru klíčů může být dlouhý 3 až 24 znaků. 

    Pro každý Azure Stack hraničního prostředku, který se aktivuje s vaším zařízením, se vytvoří Trezor klíčů. Trezor klíčů umožňuje ukládat a přistupovat k tajným klíčům, například klíč integrity kanálu (CIK) pro službu, je uložený v trezoru klíčů. 

    Jakmile zadáte název trezoru klíčů, vyberte **vygenerovat klíč** a vytvořte aktivační klíč. 

    ![Získání aktivačního klíče](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

    Počkejte několik minut, než se vytvoří Trezor klíčů a aktivační klíč. Vyberte ikonu kopírování a zkopírujte klíč a uložte ho pro pozdější použití.


> [!IMPORTANT]
> - Aktivační klíč vyprší tři dny po vygenerování.
> - Pokud vypršela platnost klíče, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o Azure Stack tématech z aplikace Edge pro, jako jsou:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Přejděte k dalšímu kurzu, kde se dozvíte, jak nainstalovat Azure Stack Edge pro.

> [!div class="nextstepaction"]
> [Nainstalovat Azure Stack Edge pro](./azure-stack-edge-gpu-deploy-install.md)




---
title: Kurz pro přípravu Azure Portalho prostředí Datacenter k nasazení Azure Stack Edge pro | Microsoft Docs
description: První kurz o nasazení Azure Stack Edge pro zahrnuje přípravu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 07b526d443b5f1b41bc6f811b7cccc0fbc6165ee
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761705"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro"></a>Kurz: Příprava na nasazení Azure Stack Edge pro  

Toto je první kurz v sérii kurzů pro nasazení, které jsou potřeba k úplnému nasazení Azure Stack Edge pro. V tomto kurzu se dozvíte, jak připravit Azure Portal k nasazení Azure Stack hraničního prostředku.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="get-started"></a>Začínáme

Pokud chcete nasadit Azure Stack Edge pro, přečtěte si následující kurzy v předepsané posloupnosti.

| **#** | **V tomto kroku** | **Použijte tyto dokumenty** |
| --- | --- | --- | 
| 1. |**[Příprava Azure Portal pro Azure Stack Edge pro](azure-stack-edge-deploy-prep.md)** |Před instalací fyzického zařízení hraničního Azure Stack box vytvořte a nakonfigurujte Azure Stack hraničního prostředku. |
| 2. |**[Nainstalovat Azure Stack Edge pro](azure-stack-edge-deploy-install.md)**|Rozbalení, stojan a kabel fyzického zařízení Azure Stack Edge pro.  |
| 3. |**[Připojení, nastavení a aktivace Azure Stack Edge pro](azure-stack-edge-deploy-connect-setup-activate.md)** |Připojte se k místnímu webovému uživatelskému rozhraní, dokončete nastavení zařízení a aktivujte zařízení. Zařízení je připravené na nastavení sdílených složek SMB nebo sdílených složek systému souborů NFS.  |
| 4. |**[Přenos dat pomocí Azure Stack Edge pro](azure-stack-edge-deploy-add-shares.md)** |Přidejte sdílené složky a připojte se k nim pomocí protokolu SMB nebo přes systém souborů NFS. |
| 5. |**[Transformace dat pomocí Azure Stack Edge pro](azure-stack-edge-deploy-configure-compute.md)** |Nakonfigurujte výpočetní moduly na zařízení, aby se data transformoval při přesunu do Azure. |

Teď můžete začít nastavovat Azure Portal.

## <a name="prerequisites"></a>Požadavky

Níže jsou uvedené požadavky na konfiguraci pro váš Azure Stack hraniční prostředek, vaše zařízení Azure Stack Edge pro a síť datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Pro prostředek Azure Stack Edge

Než začnete, ujistěte se, že:

* Ve vašem předplatném Microsoft Azure je povolený prostředek Azure Stack Edge. Ujistěte se, že jste použili podporované předplatné, jako je například [Microsoft smlouva Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp)nebo [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Předplatná s průběžnými platbami se nepodporují.

* Máte přístup vlastníka nebo přispěvatele na úrovni skupiny prostředků pro Azure Stack prostředky hraniční/Data Box Gateway, IoT Hub a Azure Storage.

  * Pro udělení přístupu přispěvatele byste měli být **vlastníkem** na úrovni předplatného. Pokud chcete někomu udělit přístup někomu jinému, v Azure Portal přejděte na **všechny služby služby**  >    >  **řízení přístupu (IAM)**  >  **+ Přidat**  >  **Přidání přiřazení role**. Další informace najdete v tématu [kurz: udělení přístupu uživatele k prostředkům Azure pomocí Azure Portal](../role-based-access-control/quickstart-assign-role-user-portal.md).

  * Pokud chcete vytvořit Azure Stack hraničního nebo Data Box Gatewayho prostředku, měli byste mít oprávnění jako Přispěvatel (nebo vyšší) vymezené na úrovni skupiny prostředků. Musíte se také ujistit, že `Microsoft.DataBoxEdge` je poskytovatel prostředků zaregistrovaný. Informace o tom, jak zaregistrovat poskytovatele prostředků, najdete v tématu [registrace poskytovatele prostředků](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Pokud chcete vytvořit libovolný prostředek IoT Hub, ujistěte se, že je poskytovatel Microsoft. Devices zaregistrovaný. Informace o registraci najdete v tématu [Registrace poskytovatele prostředků](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Pokud chcete vytvořit prostředek účtu úložiště, musíte pro něj znovu použít rozsah přístupu přispěvatele nebo vyšší úrovně. Azure Storage je ve výchozím nastavení registrovaný poskytovatel prostředků.
* K Graph API Azure Active Directory máte oprávnění správce nebo uživatel. Další informace najdete v tématu [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Máte účet služby Microsoft Azure Storage a přihlašovací údaje účtu.
* Žádné zásady Azure nastavené vaším správcem systému neblokuje. Další informace o zásadách najdete v tématu [rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy](../governance/policy/assign-policy-portal.md).

### <a name="for-the-azure-stack-edge-pro-device"></a>Pro zařízení Azure Stack Edge pro

Před nasazením fyzického zařízení se ujistěte, že:

* Zkontrolovali jste bezpečnostní údaje, které byly součástí balíčku dodávek.
* Máte slot 1U, který je ve vašem datacentru k dispozici ve standardu 19 "Rack pro připojení zařízení.
* Máte přístup k plochému, stabilnímu a pracovnímu pracovnímu prostoru, kde může zařízení bezpečně klidovat.
* Lokalita, ve které máte v úmyslu zařízení nastavit, má standardní sílu napájení z nezávislého zdroje nebo jednotky PDU (AC) (napájení) s nepřerušitelným zdrojem napájení (UPS).
* Máte přístup k fyzickému zařízení.

### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

* Síť v datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Azure Stack Edge pro. Další informace najdete v tématu [požadavky na systém Azure Stack Edge pro](azure-stack-edge-system-requirements.md).

* Pro normální provozní podmínky vaší Azure Stack Edge pro máte následující:

  * Minimální šířka pásma ke stažení, která zajistí, že se zařízení bude aktualizovat, musí mít minimálně 10 MB/s.
  * Minimálně 20 MB/s vyhrazeným nahráváním a šířkou pásma pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud máte existující Azure Stack hraniční prostředek pro správu fyzického zařízení, přeskočte tento krok a přejděte na [získat aktivační klíč](#get-the-activation-key).

Chcete-li vytvořit prostředek Azure Stack Edge, proveďte v Azure Portal následující kroky.

1. Použití přihlašovacích údajů pro Microsoft Azure pro přihlášení k 

    - Azure Portal na této adrese URL: [https://portal.azure.com](https://portal.azure.com) .
    - Nebo Azure Government portál na této adrese URL: [https://portal.azure.us](https://portal.azure.us) . Další podrobnosti najdete na webu [připojení k Azure Government pomocí portálu](../azure-government/documentation-government-get-started-connect-with-portal.md).

2. V levém podokně vyberte **+ vytvořit prostředek**. Vyhledejte a vyberte **Azure Stack Edge/data box Gateway**. Vyberte **Vytvořit**.
3. Vyberte předplatné, které chcete použít pro zařízení Azure Stack Edge pro. Vyberte oblast, ve které chcete nasadit prostředek Azure Stack Edge. Seznam všech oblastí, kde jsou k dispozici prostředky Azure Stack Edge, najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

    Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. V oblasti jsou uložena pouze metadata pro správu zařízení. Skutečná data můžou být uložená v jakémkoli účtu úložiště.
    
    V možnosti **Azure Stack Edge pro** vyberte **vytvořit**.

    ![Hledat Azure Stack hraniční služby](media/azure-stack-edge-deploy-prep/data-box-edge-sku.png)

3. Na kartě **základy** zadejte nebo vyberte následující **Podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |Tato hodnota se vyplní automaticky na základě dřívějšího výběru. Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).     |

4. Zadejte nebo vyberte následující **Podrobnosti o instanci**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název   | Popisný název pro identifikaci prostředku.<br>Název má 2 až 50 znaků, včetně písmen, číslic a spojovníků.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Region (Oblast)     |Seznam všech oblastí, kde jsou k dispozici prostředky Azure Stack Edge, najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Pokud používáte Azure Government, jsou dostupné všechny oblasti státní správy, jak je znázorněno v [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|

    ![Podrobnosti o projektu a instanci](media/azure-stack-edge-deploy-prep/data-box-edge-resource.png)

5. Vyberte **Další: adresa příjemce**.

    - Pokud už máte nějaké zařízení, vyberte pole se seznamem, kde **mám Azure Stack hraniční zařízení**.
    - Pokud se jedná o nové zařízení, které objednáváte, zadejte jméno kontaktu, společnost, adresu pro odeslání zařízení a kontaktní údaje.

    ![Dodací adresa pro nové zařízení](media/azure-stack-edge-deploy-prep/data-box-edge-resource1.png)

6. Vyberte **Další: Zkontrolovat a vytvořit**.

7. Na kartě **Revize + vytvořit** si přečtěte **Podrobnosti o cenách**, **podmínky použití** a podrobnosti k vašemu prostředku. Zaškrtněte pole se seznamem pro **uživatele, kteří si prošli podmínka ochrany osobních údajů**.

    ![Kontrola informací o Azure Stackch a zásadách ochrany osobních údajů z pohledu na Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource2.png)

8. Vyberte **Vytvořit**.

   Vytvoření prostředku trvá několik minut. Po úspěšném vytvoření a nasazení prostředku budete upozorněni. Vyberte **Přejít k prostředku**.

   ![Přejít na prostředek Azure Stack Edge](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Po uvedení objednávky Microsoft zkontroluje pořadí a zavede vám (prostřednictvím e-mailu) informace o expedici.

![Oznámení na revizi objednávky Azure Stack Edge pro](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)


> [!NOTE]
> Pokud chcete vytvořit více objednávek najednou nebo naklonovat stávající objednávku, můžete použít [skripty v ukázkách Azure](https://github.com/Azure-Samples/azure-stack-edge-order). Další informace najdete v souboru READme.

## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Až bude prostředek Azure Stack Edge v provozu, budete muset získat aktivační klíč. Tento klíč se používá k aktivaci a připojení zařízení Azure Stack Edge pro k prostředku. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Přejít na prostředek, který jste vytvořili, a vyberte **Přehled**. Zobrazí se oznámení o tom, jak se vaše objednávka zpracovává.

    ![Vybrat přehled](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

2. Po zpracování objednávky a na zařízení se bude **Přehled** aktualizovat. Přijměte výchozí **název Azure Key Vault** nebo zadejte nový. Vyberte možnost **Generovat aktivační klíč**. Vyberte ikonu kopírování a zkopírujte klíč a uložte ho pro pozdější použití.

    ![Získání aktivačního klíče](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * Aktivační klíč vyprší tři dny po vygenerování.
> * Pokud vypršela platnost klíče, vygenerujte nový klíč. Starší klíč není platný.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o Azure Stack tématech z aplikace Edge pro, jako jsou:

> [!div class="checklist"]
>
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

Přejděte k dalšímu kurzu, kde se dozvíte, jak nainstalovat Azure Stack Edge pro.

> [!div class="nextstepaction"]
> [Nainstalovat Azure Stack Edge pro](./azure-stack-edge-deploy-install.md)
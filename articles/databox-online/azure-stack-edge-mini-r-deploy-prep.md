---
title: Kurz pro přípravu Azure Portal a prostředí Datacenter k nasazení Azure Stack hraničních zařízení R | Microsoft Docs
description: První kurz o nasazení Azure Stackového Mininího zařízení R zahrnuje přípravu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Mini R device so I can use it to transfer data to Azure.
ms.openlocfilehash: ed11b0bb00a571fb4cefc51a708432baef88184d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613069"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Kurz: Příprava na nasazení Azure Stack hraniční Mini R

Tento kurz je první v sérii kurzů pro nasazení, které jsou potřeba k úplnému nasazení Azure Stackho Miniho zařízení R. V tomto kurzu se dozvíte, jak připravit Azure Portal k nasazení Azure Stack hraničního prostředku.

K dokončení této instalace a procesu konfigurace potřebujete oprávnění správce. Příprava portálu trvá méně než 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nového prostředku
> * Získání aktivačního klíče

### <a name="get-started"></a>Začínáme

Pokud chcete nasadit Azure Stack hraničního Mini R, přečtěte si následující kurzy v předepsané posloupnosti.

| Krok | Description |
| --- | --- |
| **Součást** |Tyto kroky je potřeba provést v přípravě na nadcházející nasazení. |
| **[Kontrolní seznam konfigurace nasazení](#deployment-configuration-checklist)** |Kontrolní seznam použijte ke shromáždění a zaznamenání informací před nasazením a během nasazení. |
| **[Požadavky nasazení](#prerequisites)** |Tyto požadavky ověřují, zda je prostředí připraveno k nasazení. |
|  | |
|**Kurzy k nasazení** |Tyto kurzy se vyžadují k nasazení Azure Stackch Mini R zařízení v produkčním prostředí. |
|**[1. Příprava Azure Portal pro zařízení](azure-stack-edge-mini-r-deploy-prep.md)** |Před instalací fyzického zařízení vytvořte a nakonfigurujte Azure Stack hraničního prostředku. |
|**[2. Nainstalujte zařízení.](azure-stack-edge-mini-r-deploy-install.md)**|Zkontrolujte a kabelujte fyzické zařízení.  |
|**[3. Připojte se k zařízení.](azure-stack-edge-mini-r-deploy-connect.md)** |Jakmile je zařízení nainstalované, připojte se k místnímu webovému uživatelskému rozhraní zařízení.  |
|**[4. Konfigurace nastavení sítě](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Nakonfigurujte síť včetně nastavení výpočetní sítě a webového proxy serveru pro vaše zařízení.   |
|**[5. Konfigurace nastavení zařízení](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Přiřaďte název zařízení a doménu DNS, nakonfigurujte server aktualizací a čas zařízení. |
|**[6. Konfigurace nastavení zabezpečení](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Konfigurace certifikátů pomocí vlastních certifikátů, nastavení sítě VPN a konfigurace místního nastavení pro vaše zařízení.   |
|**[7. aktivace zařízení](azure-stack-edge-mini-r-deploy-activate.md)** |K aktivaci zařízení použijte aktivační klíč ze služby. Zařízení je připravené nastavovat sdílené složky SMB nebo NFS nebo je připojit prostřednictvím REST. |
|**[8. konfigurace COMPUTE](azure-stack-edge-gpu-deploy-configure-compute.md)** |Nakonfigurujte na svém zařízení výpočetní roli. Vytvoří se také cluster Kubernetes. |

Teď můžete začít nastavovat Azure Portal.

## <a name="deployment-configuration-checklist"></a>Kontrolní seznam konfigurace nasazení

Před nasazením zařízení je potřeba shromáždit informace ke konfiguraci softwaru na Azure Stack hraničním zařízení s hraničním konektorem R. Když se některé z těchto informací předem připravují, pomůže vám to zjednodušit proces nasazení zařízení ve vašem prostředí. Pomocí [kontrolního seznamu konfigurace nasazení Azure Stackho Edge Mini R](azure-stack-edge-mini-r-deploy-checklist.md) si poznamenejte podrobnosti konfigurace při nasazování zařízení.

## <a name="prerequisites"></a>Předpoklady

Níže jsou uvedené požadavky na konfiguraci pro váš Azure Stack hraniční zdroj, vaše zařízení Azure Stack Edge a síť datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Pro prostředek Azure Stack Edge

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Pro zařízení Azure Stack Edge

Před nasazením fyzického zařízení se ujistěte, že:

- V [bezpečnostních pokynech pro vaše Azure Stack hraniční zařízení](azure-stack-edge-mini-r-safety.md)jste zkontrolovali bezpečnostní informace pro toto zařízení.
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Síť datacentra

Než začnete, ujistěte se, že:

- Síť v datacentru je nakonfigurovaná podle požadavků na síť pro vaše zařízení Azure Stack Edge. Další informace najdete v článku [požadavky na systém Azure Stack Edge Mini R](azure-stack-edge-mini-r-system-requirements.md).

- Pro normální provozní podmínky Azure Stack hraničního zařízení máte následující:

    - Minimální šířka pásma ke stažení, která zajistí, že se zařízení bude aktualizovat, musí mít minimálně 10 MB/s.
    - Minimálně 20 MB/s vyhrazeným nahráváním a šířkou pásma pro přenos souborů.

## <a name="create-a-new-resource"></a>Vytvoření nového prostředku

Pokud máte existující Azure Stack hraniční prostředek pro správu fyzického zařízení, přeskočte tento krok a přejděte na [získat aktivační klíč](#get-the-activation-key).

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Chcete-li vytvořit prostředek Azure Stack Edge, proveďte v Azure Portal následující kroky.

1. Pomocí přihlašovacích údajů pro Microsoft Azure se přihlaste k Azure Portal na této adrese URL: [https://portal.azure.com](https://portal.azure.com) .


2. V levém podokně vyberte **+ Create a resource** (+ Vytvořit prostředek). Vyhledejte a vyberte **Azure Stack Edge/data box Gateway**. Vyberte **Vytvořit**. 

3. Vyberte předplatné, které chcete použít pro zařízení Azure Stack Edge pro. Vyberte zemi, do které chcete toto fyzické zařízení dodávat. Vyberte **Zobrazit zařízení**.

    ![Vytvoření prostředku 1](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. Vyberte typ zařízení. V části **Azure Stack Edge** zvolte **Azure Stack Edge Mini R** a pak zvolte **Vybrat**. Pokud se zobrazí nějaké problémy nebo nemůžete vybrat typ zařízení, přejděte k tématu [řešení potíží s pořadím](azure-stack-edge-troubleshoot-ordering.md).

    [![Vytvoření prostředku 2](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


5. Na kartě **základy** zadejte nebo vyberte následující **Podrobnosti o projektu**.
    
    |Nastavení  |Hodnota  |
    |---------|---------|
    |Předplatné    |Předplatné se automaticky vyplní na základě dřívějšího výběru. Předplatné je propojené s vaším fakturačním účtem. |
    |Skupina prostředků  |Vyberte existující skupinu nebo vytvořte novou.<br>Další informace o [skupinách prostředků Azure](../azure-resource-manager/management/overview.md).     |


6. Zadejte nebo vyberte následující **Podrobnosti o instanci**.

    |Nastavení  |Hodnota  |
    |---------|---------|
    |Název   | Popisný název pro identifikaci prostředku.<br>Název má 2 až 50 znaků včetně písmen, číslic a spojovníků.<br> Název musí začínat a končit na písmeno nebo číslici.        |
    |Oblast     |Seznam všech oblastí, kde jsou k dispozici prostředky Azure Stack Edge, najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Pokud používáte Azure Government, jsou dostupné všechny oblasti státní správy, jak je znázorněno v [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit.|

    ![Vytvoření prostředku 4](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


7. Vyberte **Další: adresa příjemce**.

   - Pokud už máte nějaké zařízení, vyberte pole se seznamem, u kterého **už zařízení máte**.

     ![Vytvoření prostředku 5](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

   - Pokud se jedná o nové zařízení, které objednáváte, zadejte jméno kontaktu, společnost, adresu, kam se má zařízení expedovat, a kontaktní údaje.

     ![Vytvoření prostředku 6](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

8. Vyberte **Další: značky**. Volitelně můžete zadat značky pro kategorizaci prostředků a konsolidaci fakturace. Vyberte **Další: Zkontrolovat a vytvořit**.

9. Na kartě **Revize + vytvořit** si přečtěte **Podrobnosti o cenách**, **podmínky použití** a podrobnosti k vašemu prostředku. Zaškrtněte pole se seznamem pro **uživatele, kteří si prošli podmínka ochrany osobních údajů**.

    ![Vytvoření prostředku 7](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png)

    Také se vám upozorní, že při vytváření prostředků je povolená služba Identita spravované služby (MSI), která umožňuje ověření pro cloudové služby. Tato identita existuje, pokud prostředek existuje.

10. Vyberte **Vytvořit**.

    Vytvoření prostředku trvá několik minut. Vytvoří se taky soubor MSI, který umožňuje, aby zařízení Azure Stack Edge komunikovalo se zprostředkovatelem prostředků v Azure.
    
    Po úspěšném vytvoření a nasazení prostředku budete upozorněni. Vyberte **Přejít k prostředku**.
    
    ![Přejít na prostředek Azure Stack Edge pro](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
Po uvedení objednávky Microsoft zkontroluje pořadí a zavede vám (prostřednictvím e-mailu) informace o expedici.

> [!NOTE]
> Pokud chcete vytvořit více objednávek najednou nebo naklonovat stávající objednávku, můžete použít [skripty v ukázkách Azure](https://github.com/Azure-Samples/azure-stack-edge-order). Další informace najdete v souboru READme.

Pokud narazíte na problémy v průběhu procesu objednávky, přečtěte si téma [řešení potíží s pořadím](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

V případě potřeby připravte prostředí pro rozhraní příkazového řádku Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Pokud chcete vytvořit prostředek Azure Stack Edge, spusťte v Azure CLI následující příkazy.

1. Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group#az_group_create) nebo použijte existující skupinu prostředků:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Zařízení vytvoříte pomocí příkazu [AZ databoxedge Device Create](/cli/azure/databoxedge/device#az_databoxedge_device_create) :

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeMR_Mini
   ```

   Zvolte umístění, které je nejblíže zeměpisné oblasti, ve které chcete zařízení nasadit. V oblasti jsou uložena pouze metadata pro správu zařízení. Skutečná data můžou být uložená v jakémkoli účtu úložiště.

   Seznam všech oblastí, kde jsou k dispozici prostředky Azure Stack Edge, najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Pokud používáte Azure Government, jsou dostupné všechny oblasti státní správy, jak je znázorněno v [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).

1. Chcete-li vytvořit objednávku, spusťte příkaz [AZ databoxedge Order Create](/cli/azure/databoxedge/order#az_databoxedge_order_create) :

   ```azurecli
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

Vytvoření prostředku trvá několik minut. Chcete-li zobrazit pořadí, spusťte příkaz [AZ databoxedge Order show](/cli/azure/databoxedge/order#az_databoxedge_order_show) :

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Po umístění objednávky Microsoft zkontroluje pořadí a kontaktuje e-mail s podrobnostmi o expedici.

---

## <a name="get-the-activation-key"></a>Získání aktivačního klíče

Až bude prostředek Azure Stack Edge v provozu, budete muset získat aktivační klíč. Tento klíč slouží k aktivaci a připojení Azure Stack hraničního zařízení R s prostředkem. Tento klíč můžete získat teď, když jste na webu Azure Portal.

1. Vyberte prostředek, který jste vytvořili, a vyberte **Přehled**.

   ![Vybrat nastavení zařízení](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. Na dlaždici **aktivovat** zadejte název pro Azure Key Vault nebo přijměte výchozí název. Název trezoru klíčů může být dlouhý 3 až 24 znaků. 

    Pro každý Azure Stack hraničního prostředku, který se aktivuje s vaším zařízením, se vytvoří Trezor klíčů. Trezor klíčů umožňuje ukládání tajných klíčů a přístup k nim. Například klíč integrity kanálu (CIK) pro službu je uložený v trezoru klíčů.

    Jakmile zadáte název trezoru klíčů, vyberte možnost **Generovat aktivační klíč** a vytvořte aktivační klíč.

    [![Získání aktivačního klíče](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

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
> [Nainstalovat Azure Stack Edge](./azure-stack-edge-mini-r-deploy-install.md)

---
title: Povolit privátní přístup pomocí privátního odkazu (Preview) – portál
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak povolit privátní přístup pro řešení digitálních vláken Azure pomocí privátního propojení pomocí Azure Portal.
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2545915edf9e39b63100a2bb16bd34fa6777675c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417374"
---
# <a name="enable-private-access-with-private-link-preview-azure-portal"></a>Povolit privátní přístup pomocí privátního odkazu (Preview): Azure Portal

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Tento článek popisuje různé způsoby, jak [Povolit privátní propojení s privátním koncovým bodem pro instanci digitálních vláken Azure](concepts-security.md#private-network-access-with-azure-private-link-preview) (v současnosti ve verzi Preview). Konfigurace privátního koncového bodu pro instanci digitálních vláken Azure vám umožní zabezpečit vaši instanci digitálních vláken Azure a eliminovat veřejnou expozici a zabránit tomu, aby exfiltrace data z [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md).

Tento článek vás provede procesem použití [**Azure Portal**](https://portal.azure.com).

Tady je postup, který je popsaný v tomto článku: 
1. Zapnout privátní odkaz a nakonfigurovat privátní koncový bod pro instanci digitálních vláken Azure
1. Zakáže nebo povolí příznaky přístupu k veřejné síti, aby se omezil přístup k rozhraním API jenom na připojení přes privátní propojení.

## <a name="prerequisites"></a>Předpoklady

Než budete moct nastavit privátní koncový bod, budete potřebovat službu [**Azure Virtual Network (VNET)**](../virtual-network/virtual-networks-overview.md) , ve které se dá koncový bod nasadit. Pokud ještě nemáte virtuální síť, můžete ji nastavit pomocí některého z [rychlých startů](../virtual-network/quick-create-portal.md) pro Azure Virtual Network.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Přidání privátního koncového bodu instance digitálního vlákna Azure 

Při použití [Azure Portal](https://portal.azure.com)se můžete rozhodnout zapnout privátní odkaz s privátním koncovým bodem pro instanci digitálního vlákna Azure v rámci počátečního nastavení instance nebo ho později povolit na instanci, která už existuje. 

Kterákoli z těchto metod vytváření poskytne stejné možnosti konfigurace a stejný konečný výsledek pro vaši instanci. Tato část popisuje, jak provést každou z nich.

>[!TIP]
> Můžete také nastavit koncový bod privátního propojení prostřednictvím služby privátního propojení namísto instance digitálního vlákna Azure. To také nabízí stejné možnosti konfigurace a stejný konečný výsledek.
>
> Další podrobnosti o nastavení prostředků privátního propojení najdete v tématu dokumentace k privátním odkazům pro [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md)nebo [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Přidání privátního koncového bodu během vytváření instance

V této části povolíte privátní odkaz s privátním koncovým bodem na instanci digitálních vláken Azure, která se právě vytváří. Tato část se zaměřuje na krokování sítě procesu vytváření; Úplný návod k vytvoření nové instance digitálního vlákna Azure najdete v tématu [*Postup: nastavení instance a ověřování*](how-to-set-up-instance-portal.md).

Možnosti privátního odkazu se nachází na kartě **síť** v nastavení instance.

Na této kartě můžete povolit privátní koncové body výběrem možnosti **privátního koncového bodu** pro **metodu připojení**.

Tím se přidá oddíl s názvem **připojení privátního koncového bodu** , kde můžete nakonfigurovat podrobnosti privátního koncového bodu. Pokračujte výběrem tlačítka **+ Přidat** .

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Snímek obrazovky Azure Portal zobrazující kartu síť dialogového okna vytvořit prostředek pro digitální vlákna Azure Existuje zvýraznění kolem názvu karty, možnosti privátního koncového bodu pro metodu připojení a tlačítka + Přidat k vytvoření nového připojení privátního koncového bodu." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

Tím se otevře stránka pro zadání podrobností nového privátního koncového bodu.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Snímek obrazovky Azure Portal se zobrazením stránky vytvořit soukromý koncový bod Obsahuje pole, která jsou popsána níže.":::

1. Vyplňte výběry pro vaše **předplatné** a **skupinu prostředků**. Nastavte **umístění** na stejné místo jako virtuální síť, kterou budete používat. Zvolte **název** koncového bodu a pro **cílové dílčí prostředky** vyberte *rozhraní API*.

1. Potom vyberte **virtuální síť** a **podsíť** , které chcete použít k nasazení koncového bodu.

1. Nakonec vyberte, jestli se má **integrovat s privátní zónou DNS**. Pro nápovědu k této možnosti můžete použít výchozí nastavení **Ano** nebo, můžete pomocí odkazu na portálu [získat další informace o integraci privátních DNS](../private-link/private-endpoint-overview.md#dns-configuration).

Po vyplnění možností konfigurace stiskněte **OK** a dokončete to.

Tím se vrátíte na kartu **síť** v nastavení instance digitálního vlákna Azure, kde by měl být nový koncový bod viditelný v části * * připojení privátního koncového bodu.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Snímek obrazovky Azure Portal zobrazující kartu síť dialogového okna vytvořit prostředek pro digitální vlákna Azure Existuje zvýraznění kolem nového připojení privátního koncového bodu a navigační tlačítka (revize + vytvořit, předchozí, další: Upřesnit)." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

Pak můžete pomocí dolních navigačních tlačítek pokračovat v práci se zbytkem nastavení instance.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Přidání privátního koncového bodu do existující instance

V této části povolíte privátní odkaz s privátním koncovým bodem pro instanci digitálních vláken Azure, která už existuje.

1. Nejprve v prohlížeči přejděte na [Azure Portal](https://portal.azure.com) . Využijte svůj název na panelu hledání na portálu, abyste si vyhledali instanci digitálních vláken Azure.

1. V nabídce na levé straně vyberte **síť (Preview)** .

1. Přepněte na kartu **připojení privátního koncového bodu** .

1. Výběrem **+ soukromý koncový bod** otevřete nastavení **vytvořit privátní koncový bod** .

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Snímek obrazovky Azure Portal zobrazující stránku sítě (Preview) pro instanci digitálních vláken Azure Karta připojení privátního koncového bodu se zvýrazní a zvýrazní se taky tlačítko + soukromý koncový bod." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. Na kartě  **základy**   Zadejte nebo vyberte **předplatné** a **skupinu prostředků** vašeho projektu a **název** a **oblast** pro koncový bod. Oblast musí být stejná jako oblast virtuální sítě, kterou používáte.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Snímek obrazovky Azure Portal zobrazující kartu první (základní) v dialogovém okně Vytvoření privátního koncového bodu. Obsahuje pole popsaná výše.":::

    Až budete hotovi, vyberte tlačítko **Další: prostředek >** , abyste přešli na další kartu.

1. Na kartě **prostředek** zadejte nebo vyberte tyto informace: 
    * **Způsob připojení**: **v adresáři vyberte připojit k prostředku Azure** a vyhledejte vaši instanci digitálních vláken Azure.
    * **Předplatné**: Zadejte své předplatné.
    * **Typ prostředku**: vyberte **Microsoft. DigitalTwins/digitalTwinsInstances** .
    * **Prostředek**: vyberte název instance digitálního vlákna Azure.
    * **Cílový dílčí prostředek**: vyberte **rozhraní API**.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Snímek obrazovky Azure Portal, na které se zobrazuje druhá karta (prostředek) v dialogovém okně Vytvoření privátního koncového bodu. Obsahuje pole popsaná výše.":::

    Až budete hotovi, vyberte tlačítko **Další: >konfigurace** , abyste přešli na další kartu.    

1. Na kartě **Konfigurace** zadejte nebo vyberte tyto informace:
    * **Virtuální síť**: Vyberte svou virtuální síť.
    * **Podsíť**: vyberte podsíť z vaší virtuální sítě.
    * **Integrace s privátní zónou DNS**: vyberte, jestli se má **integrovat s privátní zónou DNS**. Pro nápovědu k této možnosti můžete použít výchozí nastavení **Ano** nebo, můžete pomocí odkazu na portálu [získat další informace o integraci privátních DNS](../private-link/private-endpoint-overview.md#dns-configuration).
    Pokud vyberete **Ano**, můžete ponechat výchozí informace o konfiguraci.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Snímek obrazovky Azure Portal zobrazující třetí (konfigurační) kartu v dialogovém okně Vytvoření privátního koncového bodu. Obsahuje pole popsaná výše.":::

    Až budete hotovi, můžete kliknutím na tlačítko **Revize + vytvořit** instalaci dokončit. 

1. Na kartě **Revize + vytvořit** zkontrolujte výběr a klikněte na tlačítko  **vytvořit** . 

Po dokončení nasazení koncového bodu by se měl zobrazit v připojeních privátního koncového bodu pro instanci digitálního vlákna Azure.

>[!TIP]
> Koncový bod lze také zobrazit z centra privátního spojení v Azure Portal.

## <a name="disable--enable-public-network-access-flags"></a>Zakázat/povolit příznaky přístupu k veřejné síti

Instanci digitálního vlákna Azure můžete nakonfigurovat tak, aby odepřela všechna veřejná připojení a povolovala jenom připojení prostřednictvím privátních koncových bodů, aby se zvýšilo zabezpečení sítě. Tato akce se provádí s **příznakem přístupu k veřejné síti**. 

Tato zásada umožňuje omezit přístup k rozhraní API jenom na připojení pomocí privátního propojení. Pokud je příznak přístupu k veřejné síti nastavený na *zakázáno*, vrátí se všechna REST API volání do roviny dat instance digitálních vláken Azure z veřejného cloudu `403, Unauthorized` . Případně, pokud je zásada nastavená na *zakázáno* a požadavek se provede prostřednictvím privátního koncového bodu, volání rozhraní API se nezdaří.

V tomto článku se dozvíte, jak aktualizovat hodnotu příznaku sítě pomocí [Azure Portal](https://portal.azure.com). Pokyny k tomu, jak to provést pomocí nástroje příkazového řádku Azure CLI nebo ARMClient, najdete v tomto článku v části [verze](how-to-enable-private-link-cli.md) rozhraní příkazového řádku.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Pokud chcete zakázat nebo povolit přístup k veřejné síti v [Azure Portal](https://portal.azure.com), otevřete portál a přejděte k instanci digitálních vláken Azure.

1. V nabídce na levé straně vyberte **síť (Preview)** .

1. Na kartě **veřejný přístup** nastavte možnost **povoluje přístup k veřejné síti** buď na **zakázáno** , nebo na **všechny sítě**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Snímek obrazovky Azure Portal zobrazující stránku sítě (Preview) pro instanci digitálních vláken Azure Karta veřejný přístup je zvýrazněna a možnost zvolit, zda má být povolen přístup do veřejné sítě, je zvýrazněna také." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

Další informace o privátním propojení pro Azure: 
* [*Co je služba privátního propojení Azure?*](../private-link/private-link-service-overview.md)
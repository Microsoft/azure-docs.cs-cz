---
title: Povolení přístupu k privátnímu webu k funkcím Azure
description: Naučte se nastavit přístup k privátním uzemnům Virtuální sítě Azure pro funkce Azure.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851278"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Kurz: Vytvoření přístupu k privátním uzemnstovatobjektům funkcí Azure

Tento kurz ukazuje, jak povolit [přístup k privátnímu webu](./functions-networking-options.md#private-site-access) pomocí funkcí Azure. Pomocí přístupu k privátní síti můžete vyžadovat, aby byl kód vaší funkce spuštěn pouze z určité virtuální sítě.

Přístup k privátní síti je užitečný ve scénářích, kdy přístup k aplikaci funkce musí být omezen na konkrétní virtuální síť. Aplikace funkce může být například použitelná pouze pro zaměstnance určité organizace nebo služby, které jsou v rámci zadané virtuální sítě (například jiná funkce Azure, virtuální počítač Azure nebo cluster AKS).

Pokud aplikace Functions potřebuje přístup k prostředkům Azure v rámci virtuální sítě nebo připojené prostřednictvím [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md), je potřeba [integrace virtuální sítě.](./functions-create-vnet.md)

V tomto kurzu se dozvíte, jak nakonfigurovat přístup k soukromému webu pro vaši aplikaci funkce:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače
> * Vytvoření služby Azure Bastion
> * Vytvoření aplikace Azure Functions
> * Konfigurace koncového bodu služby virtuální sítě
> * Vytvoření a nasazení funkce Azure
> * Vyvolání funkce zvenčí a uvnitř virtuální sítě

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="topology"></a>Topologie

Následující diagram znázorňuje architekturu řešení, které má být vytvořeno:

![Diagram architektury vysoké úrovně pro řešení přístupu k privátním uvažovacím sítím](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu je důležité, abyste pochopili adresování IP adres a podsíťování. Můžete začít s [tímto článkem, který pokrývá základy adresování a podsíťování](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Mnoho dalších článků a videí jsou k dispozici on-line.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Prvním krokem v tomto kurzu je vytvoření nového virtuálního počítače uvnitř virtuální sítě.  Virtuální počítač se použije pro přístup k vaší funkci, jakmile omezíte, že je přístup k dispozici pouze z virtuální sítě.

1. Vyberte tlačítko **Vytvořit zdroj.**

2. Do vyhledávacího pole `Windows Server`zadejte a ve výsledcích hledání vyberte **systém Windows Server.**

3. Ze seznamu možností systému Windows Server vyberte **Datové centrum systému Windows Server 2019** a stiskněte tlačítko **Vytvořit.**

4. Na kartě **Základy** použijte nastavení virtuálního počítače, jak je uvedeno v tabulce pod obrázkem:

    >[!div class="mx-imgBorder"]
    >![Karta Základy pro nový virtuální virtuální účet systému Windows](./media/functions-create-private-site-access/create-vm-3.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým jsou vytvořeny prostředky. |
    | [**Skupina prostředků**](../azure-resource-manager/management/overview.md) | myResourceGroup | Zvolte skupinu prostředků, která bude obsahovat všechny prostředky pro tento kurz.  Použití stejné skupiny prostředků usnadňuje vyčištění prostředků po dokončení tohoto kurzu. |
    | **Název virtuálního počítače** | myVM | Název virtuálního_ virtuálního_ |
    | [**Oblasti**](https://azure.microsoft.com/regions/) | (USA) Střední severní USA | Vyberte oblast ve vašem okolí nebo v blízkosti funkcí, ke které chcete přistupovat. |
    | **Veřejné příchozí porty** | Žádný | Vyberte **Žádný,** abyste zajistili, že z internetu není žádné příchozí připojení k virtuálnímu virtuálnímu zařízení. Vzdálený přístup k virtuálnímu počítači se nakonfiguruje prostřednictvím služby Azure Bastion. |

5. Zvolte kartu **Síť** a vyberte **Vytvořit nový** pro konfiguraci nové virtuální sítě.

    >[!div class="mx-imgBorder"]
    >![Vytvoření nové virtuální sítě pro nový virtuální počítač](./media/functions-create-private-site-access/create-vm-networking.png)

6. V **části Vytvořit virtuální síť**použijte nastavení v tabulce pod obrázkem:

    >[!div class="mx-imgBorder"]
    >![Vytvoření nové virtuální sítě pro nový virtuální počítač](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Název** | síť myResourceGroup-vnet | Můžete použít výchozí název generovaný pro vaši virtuální síť. |
    | **Rozsah adres** | 10.10.0.0/16 | Pro virtuální síť použijte jeden rozsah adres. |
    | **Název podsítě** | Kurz | Název podsítě. |
    | **Rozsah adres** (podsíť) | 10.10.1.0/24 | Velikost podsítě definuje, kolik rozhraní lze do podsítě přidat. Tato podsíť se používá virtuálním virtuálním můlového virtuálním můlového virtuálním můlového a virtuálního soudu. Podsíť `/24` obsahuje 254 adres hostitele. |

7. Chcete-li vytvořit virtuální síť, vyberte **OK.**
8. Zpět na kartě **Síť** zkontrolujte, zda je pro **veřejnou IP adresu**vybrána možnost **Žádný** .
9. Zvolte kartu **Správa** a pak v **účtu diagnostického úložiště**zvolte Vytvořit **nový** a vytvořte nový účet úložiště.
10. Ponechte výchozí hodnoty pro **oddíly Identita**, **Automatické vypnutí**a **Zálohování.**
11. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **Vytvořit**. Proces vytváření virtuálního soudu trvá několik minut.

## <a name="configure-azure-bastion"></a>Konfigurace Azure Bastion

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) je plně spravovaná služba Azure, která poskytuje zabezpečený přístup RDP a SSH k virtuálním počítačům přímo z portálu Azure. Pomocí služby Azure Bastion odeberete potřebu konfigurovat nastavení sítě související s přístupem k protokolu RDP.

1. Na portálu zvolte **Přidat** v horní části zobrazení skupiny prostředků.
2. Do vyhledávacího pole zadejte "Bastion".  Vyberte "Bašta".
3. Vyberte **Vytvořit** a začněte proces vytváření nového prostředku Azure Bastion. V části **Virtuální síť** si všimnete chybové zprávy, protože ještě neexistuje `AzureBastionSubnet` podsíť. Podsíť je vytvořena v následujících krocích. Použijte nastavení v tabulce pod obrázkem:

    >[!div class="mx-imgBorder"]
    >![Zahájení vytváření Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Název** | myBastion | Název nového zdroje Bašty |
    | **Oblasti** | USA – středosever | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |
    | **Virtuální síť** | síť myResourceGroup-vnet | Virtuální síť, ve které bude prostředek Bastion vytvořen v |
    | **Podsíť** | AzureBastionSubnet | Podsíť ve vaší virtuální síti, do které bude nasazen nový prostředek hostitele Bastion. Je nutné vytvořit podsíť pomocí `AzureBastionSubnet`hodnoty názvu . Tato hodnota umožňuje Azure vědět, které podsítě nasadit prostředky Bastion. Musíte použít podsíť alespoň `/27` nebo větší`/27` `/26`( , , a tak dále). |

    > [!NOTE]
    > Podrobný podrobný průvodce vytvořením prostředku Azure Bastion najdete v kurzu [Vytvoření hostitele Azure Bastion.](../bastion/bastion-create-host-portal.md)

4. Vytvořte podsíť, ve které Azure může zřídit hostitele Azure Bastion. Když zvolíte **Spravovat konfiguraci podsítě,** otevře se nové podokno, ve kterém můžete definovat novou podsíť.  Chcete-li vytvořit novou podsíť, zvolte **+ podsíť.**
5. Podsíť musí mít název `AzureBastionSubnet` a předpona podsítě `/27`musí být alespoň .  Chcete-li vytvořit podsíť, vyberte **OK.**

    >[!div class="mx-imgBorder"]
    >![Vytvoření podsítě pro hostitele Azure Bastion](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. Na stránce **Vytvořit baštu** vyberte nově `AzureBastionSubnet` vytvořené ze seznamu dostupných podsítí.

    >[!div class="mx-imgBorder"]
    >![Vytvoření hostitele Azure Bastion s konkrétní podsítí](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Vyberte **zkontrolovat & vytvořit**. Po dokončení ověření vyberte **Vytvořit**. Bude trvat několik minut pro prostředek Azure Bastion, který se má vytvořit.

## <a name="create-an-azure-functions-app"></a>Vytvoření aplikace Azure Functions

Dalším krokem je vytvoření aplikace funkcí v Azure pomocí [plánu Spotřeba](functions-scale.md#consumption-plan). Kód funkce nasadit do tohoto prostředku později v kurzu.

1. Na portálu zvolte **Přidat** v horní části zobrazení skupiny prostředků.
2. Vybrat **aplikaci Výpočetní > funkce**
3. V části **Základy** použijte nastavení aplikace funkcí, jak je uvedeno v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Skupina prostředků** | myResourceGroup | Zvolte skupinu prostředků, která bude obsahovat všechny prostředky pro tento kurz.  Použití stejné skupiny prostředků pro aplikaci funkcí a virtuální ho virtuálního uživatele usnadňuje vyčištění prostředků po dokončení tohoto kurzu. |
    | **Název aplikace function** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou a-z (malá a velká písmena), 0-9 a -. |
    | **Publikovat** | kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. |
    | **Oblasti** | USA – středosever | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |

    Vyberte tlačítko **Další: Hostování >.**
4. V části **Hostování** vyberte správný **účet úložiště**, **operační systém**a **naplánujte** tak popisv následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Účet úložiště** | Globálně jedinečný název | Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete také použít existující účet, který musí splňovat [požadavky na účet úložiště](./functions-scale.md#storage-account-requirements). |
    | **Operační systém** | Preferovaný operační systém | Operační systém je předem vybrán na základě výběru zásobníku za běhu, ale v případě potřeby můžete nastavení změnit. |
    | **Plánování** | Využití | [Plán hostování](./functions-scale.md) určuje, jak je aplikace funkce škálovat a prostředky, které jsou k dispozici pro každou instanci. |
5. Chcete-li zkontrolovat výběr konfigurace aplikace, vyberte **zkontrolovat + vytvořit.**
6. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

## <a name="configure-access-restrictions"></a>Konfigurace omezení přístupu

Dalším krokem je konfigurace [omezení přístupu,](../app-service/app-service-ip-restrictions.md) aby bylo zajištěno, že pouze prostředky ve virtuální síti mohou vyvolat funkci.

Přístup k [privátní síti](functions-networking-options.md#private-site-access) je povolen vytvořením [koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) Azure Virtual Network mezi aplikací funkce a zadanou virtuální sítí. Omezení přístupu jsou implementována prostřednictvím koncových bodů služby. Koncové body služby zajišťují, že k určenému prostředku mají přístup pouze přenosy pocházející ze zadané virtuální sítě. V tomto případě určený prostředek je funkce Azure.

1. V aplikaci funkce přejděte na kartu **Networking** **Funkce platformy.** *Networking*
2. Stránka **Stav síťových funkcí** je výchozím bodem konfigurace Azure Front Door, Azure CDN a také omezení přístupu. Chcete-li konfigurovat přístup k privátnímu webu, vyberte **konfigurovat omezení přístupu** k přístupu k soukromým sítím.
3. Na stránce **Omezení přístupu** se zobrazí pouze výchozí omezení na místě. Výchozí nastavení neomezuje přístup k aplikaci funkce.  Vyberte **Přidat pravidlo** a vytvořte konfiguraci omezení přístupu k privátnímu webu.
4. V podokně **Přidat omezení přístupu** vyberte v rozevíracím poli **Typ** **virtuální síť** a pak vyberte dříve vytvořenou virtuální síť a podsíť.
5. Stránka **Omezení přístupu** nyní ukazuje, že existuje nové omezení. Může trvat několik sekund, než se `Disabled` stav `Provisioning` `Enabled` **koncového bodu** změní z do .

    >[!IMPORTANT]
    > Každá aplikace funkcí má [web S propokročilé (Kudu),](../app-service/app-service-ip-restrictions.md#scm-site) který se používá ke správě nasazení aplikací funkcí. K této stránce se přistupuje z adresy URL, například: `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Vzhledem k tomu, že omezení přístupu nebyla povolena na tomto webu nasazení, můžete stále nasadit kód projektu z místní vývojářské pracovní stanice nebo vytvářet služby bez nutnosti zřizování agenta v rámci virtuální sítě.

## <a name="access-the-functions-app"></a>Přístup k aplikaci funkce

1. Vraťte se do dříve vytvořené aplikace funkce.  V části **Přehled** zkopírujte adresu URL.

    >[!div class="mx-imgBorder"]
    >![Získání adresy URL aplikace Function](./media/functions-create-private-site-access/access-function-overview.png)

2. Pokud se pokusíte získat přístup k aplikaci funkce nyní z vašeho počítače mimo virtuální síť, obdržíte stránku HTTP 403 označující, že aplikace je zastavena.  Aplikace není zastavena. Odpověď je ve skutečnosti http 403 IP zakázaný stav.
3. Teď budete mít přístup ke své funkci z dříve vytvořeného virtuálního počítače, který je připojený k vaší virtuální síti. Abyste mohli získat přístup k webu z virtuálního počítače, budete se muset připojit k virtuálnímu počítači prostřednictvím služby Azure Bastion.  Nejdřív vyberte **Připojit** a pak zvolte **Bastion**.
4. Zadejte požadované uživatelské jméno a heslo pro přihlášení do virtuálního počítače.  Vyberte **Connect** (Připojit). Objeví se nové okno prohlížeče, které vám umožní pracovat s virtuálním počítačem.
5. Vzhledem k tomu, že tento virtuální počítač přistupuje k funkci prostřednictvím virtuální sítě, je možné přistupovat k webu z webového prohlížeče na virtuálním počítači.  Je důležité si uvědomit, že zatímco aplikace funkce je přístupná pouze z určené virtuální sítě, veřejná položka DNS zůstane. Jak je uvedeno výše, pokus o přístup k webu bude mít za následek odpověď HTTP 403.

## <a name="create-a-function"></a>Vytvoření funkce

Dalším krokem v tomto kurzu je vytvoření funkce Azure spouštěné http. Vyvolání funkce prostřednictvím HTTP GET nebo POST by mělo mít za následek odpověď "Hello, {name}".  

1. Podle jednoho z následujících rychlých startů vytvořte a nasaďte aplikaci Azure Functions.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Příkazový řádek](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Při publikování projektu Azure Functions zvolte prostředek aplikace funkce, který jste vytvořili dříve v tomto kurzu.
3. Ověřte, zda je funkce nasazena.

    >[!div class="mx-imgBorder"]
    >![Nasazená funkce v seznamu funkcí](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Vyvolání funkce přímo

1. Chcete-li otestovat přístup k funkci, musíte zkopírovat adresu URL funkce. Vyberte nasazenou funkci a pak vyberte **adresu URL funkce</> Get**. Kliknutím na tlačítko **Kopírovat** zkopírujte adresu URL do schránky.

    >[!div class="mx-imgBorder"]
    >![Kopírování adresy URL funkce](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Při spuštění funkce se na portálu zobrazí chyba za běhu, která uvádí, že runtime funkce nelze spustit. Navzdory textu zprávy je aplikace funkce skutečně spuštěna. Chyba je výsledkem nových omezení přístupu, které brání portálu dotazování ke kontrole v době runtime.

2. Vložte adresu URL do webového prohlížeče. Když se nyní pokusíte získat přístup k aplikaci funkce z počítače mimo virtuální síť, obdržíte odpověď HTTP 403 označující, že aplikace je zastavena.

## <a name="invoke-the-function-from-the-virtual-network"></a>Vyvolání funkce z virtuální sítě

Přístup k funkci prostřednictvím webového prohlížeče (pomocí služby Azure Bastion) na nakonfigurovaném virtuálním počítači ve virtuální síti má za následek úspěch!

>[!div class="mx-imgBorder"]
>![Přístup k funkci Azure přes Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky


> [!div class="nextstepaction"]
> [Další informace o možnostech vytváření sítí ve funkcích](./functions-networking-options.md)

---
title: Povolit přístup k privátnímu webu pro Azure Functions
description: Naučte se nastavit přístup k privátní lokalitě Azure Virtual Network pro Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: 766ad12daeb6d2763f7ed5fe026cd4a0021eaf33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937038"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Kurz: vytvoření přístupu k privátnímu webu Azure Functions

V tomto kurzu se dozvíte, jak povolit [přístup k privátní lokalitě](./functions-networking-options.md#private-endpoint-connections) pomocí Azure Functions. Pomocí privátního přístupu k webu můžete vyžadovat, aby se kód vaší funkce aktivoval jenom z konkrétní virtuální sítě.

Přístup k privátní lokalitě je užitečný ve scénářích, kdy se přístup k aplikaci Function App musí omezovat na konkrétní virtuální síť. Například aplikace Function App se může uplatňovat jenom na zaměstnance konkrétní organizace nebo na služby, které jsou v rámci zadané virtuální sítě (například jiné funkce Azure, virtuální počítač Azure nebo cluster AKS).

Pokud aplikace Functions potřebuje přístup k prostředkům Azure v rámci virtuální sítě nebo připojená prostřednictvím [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md), je potřeba [Integrace virtuální sítě](./functions-create-vnet.md) .

V tomto kurzu se dozvíte, jak nakonfigurovat přístup k privátnímu webu pro aplikaci Function App:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače
> * Vytvoření služby Azure bastionu
> * Vytvoření aplikace Azure Functions
> * Konfigurace koncového bodu služby virtuální sítě
> * Vytvoření a nasazení funkce Azure Functions
> * Vyvolat funkci z vnějšku a v rámci virtuální sítě

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="topology"></a>Topologie

Následující diagram znázorňuje architekturu řešení, které se má vytvořit:

![Diagram architektury vysoké úrovně pro řešení přístupu k soukromému webu](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Požadavky

Pro účely tohoto kurzu je důležité pochopit IP adresy a podsítě. Můžete začít s [tímto článkem, který se zabývá základy adresování a podsítí](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Mnoho dalších článků a videí je k dispozici online.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Prvním krokem v tomto kurzu je vytvoření nového virtuálního počítače ve virtuální síti.  Po omezení přístupu k vaší funkci se virtuální počítač bude používat jenom v případě, že je k dispozici jenom v rámci virtuální sítě.

1. Vyberte tlačítko **vytvořit prostředek** .

1. Do vyhledávacího pole zadejte **Windows Server** a ve výsledcích hledání vyberte **Windows Server** .

1. V seznamu možností Windows serveru vyberte **Windows server 2019 Datacenter** a klikněte na tlačítko **vytvořit** .

1. Na kartě _základy_ použijte nastavení virtuálního počítače, jak je uvedeno v tabulce pod obrázkem:

    >[!div class="mx-imgBorder"]
    >![Karta základy pro nový virtuální počítač s Windows](./media/functions-create-private-site-access/create-vm-3.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | _Předplatné_ | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. |
    | [_Skupina prostředků_](../azure-resource-manager/management/overview.md) | myResourceGroup | Vyberte skupinu prostředků, která bude obsahovat všechny prostředky pro tento kurz.  Použití stejné skupiny prostředků usnadňuje vyčištění prostředků v případě, že jste s tímto kurzem hotovi. |
    | _Název virtuálního počítače_ | myVM | Název virtuálního počítače musí být ve skupině prostředků jedinečný. |
    | [_Oblast_](https://azure.microsoft.com/regions/) | VYLEPŠENÍ Střed USA – sever | Vyberte oblast poblíž nebo poblíž funkcí, ke kterým se chcete dostat. |
    | _Veřejné příchozí porty_ | Žádné | Pokud chcete zajistit, aby virtuální počítač neobsahoval žádné příchozí připojení z Internetu, vyberte možnost **žádné** . Vzdálený přístup k virtuálnímu počítači se nakonfiguruje prostřednictvím služby Azure bastionu. |

1. Zvolte kartu _síť_ a vyberte **vytvořit novou** a nakonfigurujte novou virtuální síť.

    >[!div class="mx-imgBorder"]
    >![Snímek obrazovky, který zobrazuje kartu "sítě" se zvýrazněnou akcí vytvořit novou v oddílu "virtuální síť".](./media/functions-create-private-site-access/create-vm-networking.png)

1. V části _vytvořit virtuální síť_ použijte nastavení v tabulce pod obrázkem:

    >[!div class="mx-imgBorder"]
    >![Vytvořit novou virtuální síť pro nový virtuální počítač](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | _Název_ | myResourceGroup – VNet | Můžete použít výchozí název vygenerovaný pro vaši virtuální síť. |
    | _Rozsah adres_ | 10.10.0.0/16 | Pro virtuální síť použijte jeden rozsah adres. |
    | _Název podsítě_ | Kurz | Název podsítě. |
    | _Rozsah adres_ (podsíť) | 10.10.1.0/24 | Velikost podsítě určuje, kolik rozhraní lze do podsítě přidat. Tuto podsíť používá virtuální počítač. Podsíť a/24 poskytuje 254 adres hostitelů. |

1. Vyberte **OK** a vytvořte virtuální síť.
1. Zpátky na kartě _sítě_ zkontrolujte, jestli není vybraná **možnost žádná** pro _veřejnou IP adresu_.
1. Zvolte kartu _Správa_ a potom v části _účet diagnostického úložiště_ zvolte **vytvořit novou** a vytvořte nový účet úložiště.
1. Ponechte výchozí hodnoty pro oddíly _identity_, _automatického vypínání_ a _zálohování_ .
1. Vyberte _Zkontrolovat a vytvořit_. Po dokončení ověření vyberte **vytvořit**. Proces vytvoření virtuálního počítače trvá několik minut.

## <a name="configure-azure-bastion"></a>Konfigurace Azure bastionu

[Azure bastionu](https://azure.microsoft.com/services/azure-bastion/) je plně spravovaná služba Azure, která poskytuje zabezpečený přístup pomocí protokolu RDP a SSH k virtuálním počítačům přímo z Azure Portal. Použití služby Azure bastionu odebere nutnost konfigurovat nastavení sítě související s přístupem RDP.

1. Na portálu vyberte v horní části zobrazení skupiny prostředků možnost **Přidat** .
1. Do vyhledávacího pole zadejte **bastionu**.
1. Ve výsledcích hledání vyberte **bastionu** .
1. Výběrem **vytvořit** zahájíte proces vytváření nového prostředku Azure bastionu. V části _virtuální síť_ se zobrazí chybová zpráva, protože ještě není AzureBastionSubnet podsíť. Podsíť se vytvoří v následujících krocích. Použijte nastavení v tabulce pod obrázkem:

    >[!div class="mx-imgBorder"]
    >![Začátek vytváření Azure bastionu](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | _Název_ | myBastion | Název nového prostředku bastionu |
    | _Oblast_ | USA – středosever | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž nebo poblíž dalších služeb, ke kterým máte přístup. |
    | _Virtuální síť_ | myResourceGroup – VNet | Virtuální síť, ve které bude prostředek bastionu vytvořen |
    | _Podsíť_ | AzureBastionSubnet | Podsíť ve virtuální síti, do které bude nasazen nový prostředek hostitele bastionu. Podsíť musíte vytvořit pomocí hodnoty název **AzureBastionSubnet**. Tato hodnota umožňuje službě Azure zjistit, do které podsítě nasadit prostředky bastionu. Je nutné použít podsíť alespoň **/27** nebo větší (/27,/26 atd.). |

    > [!NOTE]
    > Podrobný návod, jak vytvořit prostředek Azure bastionu, najdete v kurzu [Vytvoření hostitele Azure bastionu](../bastion/tutorial-create-host-portal.md) .

1. Vytvořte podsíť, ve které může Azure zřídit hostitele Azure bastionu. Zvolením **možnosti spravovat konfiguraci podsítě** otevřete nové podokno, kde můžete definovat novou podsíť.  Vyberte **+ podsíť** a vytvořte novou podsíť.
1. Podsíť musí mít název **AzureBastionSubnet** a předpona podsítě musí být aspoň **/27**.  Vyberte **OK** a vytvořte podsíť.

    >[!div class="mx-imgBorder"]
    >![Vytvoření podsítě pro hostitele Azure bastionu](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. Na stránce _vytvořit bastionu_ vyberte ze seznamu dostupných podsítí nově vytvořená **AzureBastionSubnet** .

    >[!div class="mx-imgBorder"]
    >![Vytvoření hostitele Azure bastionu s určitou podsítí](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. Vyberte **zkontrolovat & vytvořit**. Po dokončení ověření vyberte **vytvořit**. Vytvoření prostředku Azure bastionu bude trvat několik minut.

## <a name="create-an-azure-functions-app"></a>Vytvoření aplikace Azure Functions

Dalším krokem je vytvoření aplikace Function App v Azure s využitím [plánu spotřeby](consumption-plan.md). Kód funkce se do tohoto prostředku nasadí později v tomto kurzu.

1. Na portálu vyberte v horní části zobrazení skupiny prostředků možnost **Přidat** .
1. Vyberte **compute > Function App**
1. V části _základy_ použijte nastavení aplikace Function App, jak je uvedeno v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | _Skupina prostředků_ | myResourceGroup | Vyberte skupinu prostředků, která bude obsahovat všechny prostředky pro tento kurz.  Použití stejné skupiny prostředků pro aplikaci Function App a virtuálního počítače usnadňuje vyčištění prostředků, pokud jste s tímto kurzem hotovi. |
    | _Název aplikace funkcí_ | Globálně jedinečný název | Název identifikující novou aplikaci funkcí. Platné znaky jsou a – z (nerozlišuje velikost písmen), 0-9 a-. |
    | _Publikovat_ | Kód | Možnost publikování souborů kódu nebo kontejneru Docker |
    | _Zásobník modulu runtime_ | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. |
    | _Oblast_ | USA – středosever | Vyberte [oblast](https://azure.microsoft.com/regions/) poblíž nebo poblíž dalších služeb, ke kterým máte přístup. |

    Vyberte tlačítko **Další: hostování >** .
1. V části _hostování_ vyberte správný _účet úložiště_, _operační systém_ a _plán_ , jak je popsáno v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | _Účet úložiště_ | Globálně jedinečný název | Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](storage-considerations.md#storage-account-requirements). |
    | _Operační systém_ | Preferovaný operační systém | Operační systém je předem vybraný pro vás na základě výběru zásobníku modulu runtime, ale v případě potřeby můžete změnit nastavení. |
    | _Plán_ | Využití | [Plán hostování](./functions-scale.md) určuje, jak se aplikace funkcí škáluje, a prostředky dostupné pro jednotlivé instance. |
1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.
1. Klikněte na možnost **Vytvořit** a zřiďte a nasaďte aplikaci funkcí.

## <a name="configure-access-restrictions"></a>Konfigurace omezení přístupu

Dalším krokem je konfigurace [omezení přístupu](../app-service/app-service-ip-restrictions.md) , aby se zajistilo, že funkce může vyvolat jenom prostředky ve virtuální síti.

Přístup k [privátní lokalitě](functions-networking-options.md#private-endpoint-connections) je povolený vytvořením [koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) Azure Virtual Network mezi aplikací funkcí a zadanou virtuální sítí. Omezení přístupu jsou implementována prostřednictvím koncových bodů služby. Koncové body služeb zajišťují, že přístup k určenému prostředku má jenom přenosy pocházející z zadané virtuální sítě. V tomto případě je určeným prostředkem funkce Azure Functions.

1. V rámci aplikace Function App vyberte odkaz **sítě** v hlavičce oddílu _Nastavení_ .
1. Stránka _síť_ je výchozím bodem, ve kterém se konfigurují přední dveře Azure, Azure CDN a také omezení přístupu.
1. Vyberte **konfigurovat omezení přístupu** pro konfiguraci přístupu k privátní lokalitě.
1. Na stránce _omezení přístupu_ se zobrazí pouze výchozí omezení. Ve výchozím nastavení se neumísťují žádná omezení přístupu do aplikace Function App.  Vyberte **Přidat pravidlo** a vytvořte konfiguraci omezení přístupu k privátní lokalitě.
1. V podokně _Přidat omezení přístupu_ zadejte _název_, _prioritu_ a _Popis_ nového pravidla.
1. V rozevíracím seznamu _typ_ vyberte **Virtual Network** , vyberte dříve vytvořenou virtuální síť a potom vyberte podsíť **kurzu** . 
    > [!NOTE]
    > Povolení koncového bodu služby může trvat několik minut.
1. Na stránce _omezení přístupu_ se teď zobrazuje nové omezení. Může to trvat několik sekund, než se _stav koncového bodu_ změní z zakázáno prostřednictvím zřizování na povoleno.

    >[!IMPORTANT]
    > Každá aplikace Function App obsahuje [Web pokročilého nástroje (Kudu)](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site) , který se používá ke správě nasazení aplikací Function App. K tomuto webu se dostanete z adresy URL, jako je: `<FUNCTION_APP_NAME>.scm.azurewebsites.net` . Povolení omezení přístupu na webu Kudu zabraňuje nasazení kódu projektu z místní pracovní stanice pro vývojáře a poté, co je v rámci virtuální sítě potřeba provést nasazení, je nutné mít agenta.

## <a name="access-the-functions-app"></a>Přístup k aplikaci Functions

1. Vraťte se do dříve vytvořené aplikace Function App.  V části _Přehled_ zkopírujte adresu URL.

    >[!div class="mx-imgBorder"]
    >![Získat adresu URL aplikace Function App](./media/functions-create-private-site-access/access-function-overview.png)

    Pokud se pokusíte získat přístup k aplikaci Function App hned z počítače mimo vaši virtuální síť, obdržíte stránku HTTP 403 s oznámením, že přístup je zakázán.
1. Vraťte se do skupiny prostředků a vyberte dříve vytvořený virtuální počítač. Aby bylo možné získat přístup k webu z virtuálního počítače, musíte se k virtuálnímu počítači připojit prostřednictvím služby Azure bastionu.
1. Vyberte **připojit** a pak zvolte **bastionu**.
1. Zadejte požadované uživatelské jméno a heslo pro přihlášení k virtuálnímu počítači.
1. Vyberte **Connect** (Připojit). Otevře se nové okno prohlížeče, které vám umožní pracovat s virtuálním počítačem.
K webu je možné přistupovat z webového prohlížeče na virtuálním počítači, protože virtuální počítač přistupuje k webu přes virtuální síť.  I když je web dostupný jenom v rámci určené virtuální sítě, zůstane veřejná položka DNS.

## <a name="create-a-function"></a>Vytvoření funkce

Dalším krokem v tomto kurzu je vytvoření funkce Azure aktivované službou HTTP. Volání funkce přes HTTP GET nebo POST by mělo mít za následek odpověď "Hello, {Name}".  

1. Pomocí jednoho z následujících rychlých startů vytvořte a nasaďte aplikaci Azure Functions.

    * [Visual Studio Code](./create-first-function-vs-code-csharp.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Příkazový řádek](./create-first-function-cli-csharp.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. Když publikujete projekt Azure Functions, vyberte prostředek Function App, který jste vytvořili dříve v tomto kurzu.
1. Ověřte, že je funkce nasazená.

    >[!div class="mx-imgBorder"]
    >![Nasazená funkce v seznamu funkcí](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Vyvolat funkci přímo

1. Aby bylo možné otestovat přístup k funkci, je nutné zkopírovat adresu URL funkce. Vyberte nasazenou funkci a pak vyberte **získat adresu URL funkce**. Potom kliknutím na tlačítko **Kopírovat** zkopírujte adresu URL do schránky.

    >[!div class="mx-imgBorder"]
    >![Zkopírujte adresu URL funkce](./media/functions-create-private-site-access/get-function-url.png)

1. Vložte adresu URL do webového prohlížeče. Když se teď pokusíte o přístup k aplikaci Function App z počítače mimo virtuální síť, dostanete odpověď HTTP 403, která indikuje, že přístup k aplikaci je zakázán.

## <a name="invoke-the-function-from-the-virtual-network"></a>Vyvolat funkci z virtuální sítě

Přístup k funkci přes webový prohlížeč (pomocí služby Azure bastionu) na konfigurovaném VIRTUÁLNÍm počítači ve virtuální síti má za následek úspěch!

>[!div class="mx-imgBorder"]
>![Přístup ke službě Azure Function prostřednictvím Azure bastionu](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky


> [!div class="nextstepaction"]
> [Další informace o možnostech sítě ve funkcích](./functions-networking-options.md)
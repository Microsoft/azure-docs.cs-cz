---
title: Povolit přístup k privátnímu webu pro Azure Functions
description: Naučte se nastavit přístup k privátní lokalitě Azure Virtual Network pro Azure Functions.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851278"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Kurz: vytvoření přístupu k privátnímu webu Azure Functions

V tomto kurzu se dozvíte, jak povolit [přístup k privátní lokalitě](./functions-networking-options.md#private-site-access) pomocí Azure Functions. Pomocí privátního přístupu k webu můžete vyžadovat, aby se kód vaší funkce aktivoval jenom z konkrétní virtuální sítě.

Přístup k privátní lokalitě je užitečný ve scénářích, kdy se přístup k aplikaci Function App musí omezovat na konkrétní virtuální síť. Například aplikace Function App se může uplatňovat jenom na zaměstnance konkrétní organizace nebo na služby, které jsou v rámci zadané virtuální sítě (například jiné funkce Azure, virtuální počítač Azure nebo cluster AKS).

Pokud aplikace Functions potřebuje přístup k prostředkům Azure v rámci virtuální sítě nebo připojená prostřednictvím [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md), je potřeba [Integrace virtuální sítě](./functions-create-vnet.md) .

V tomto kurzu se dozvíte, jak nakonfigurovat přístup k privátnímu webu pro aplikaci Function App:

> [!div class="checklist"]
> * Vytvořit virtuální počítač
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

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Vytvořit virtuální počítač

Prvním krokem v tomto kurzu je vytvoření nového virtuálního počítače ve virtuální síti.  Po omezení přístupu k vaší funkci se virtuální počítač bude používat jenom v případě, že je k dispozici jenom v rámci virtuální sítě.

1. Vyberte tlačítko **vytvořit prostředek** .

2. Do vyhledávacího pole zadejte `Windows Server`a ve výsledcích hledání vyberte **Windows Server** .

3. V seznamu možností Windows serveru vyberte **Windows server 2019 Datacenter** a klikněte na tlačítko **vytvořit** .

4. Na kartě **základy** použijte nastavení virtuálního počítače, jak je uvedeno v tabulce pod obrázkem:

    >[!div class="mx-imgBorder"]
    >karta základy ![nového virtuálního počítače s Windows](./media/functions-create-private-site-access/create-vm-3.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Předplatné** | Vaše předplatné | Předplatné, ve kterém se vaše prostředky vytvářejí. |
    | [**Skupina prostředků**](../azure-resource-manager/management/overview.md) | myResourceGroup | Vyberte skupinu prostředků, která bude obsahovat všechny prostředky pro tento kurz.  Použití stejné skupiny prostředků usnadňuje vyčištění prostředků v případě, že jste s tímto kurzem hotovi. |
    | **Název virtuálního počítače** | myVM | Název virtuálního počítače musí být ve skupině prostředků jedinečný. |
    | [**Věřitel**](https://azure.microsoft.com/regions/) | VYLEPŠENÍ Střed USA – sever | Vyberte oblast poblíž nebo poblíž funkcí, ke kterým se chcete dostat. |
    | **Veřejné příchozí porty** | Žádné | Pokud chcete zajistit, aby virtuální počítač neobsahoval žádné příchozí připojení z Internetu, vyberte možnost **žádné** . Vzdálený přístup k virtuálnímu počítači se nakonfiguruje prostřednictvím služby Azure bastionu. |

5. Zvolte kartu **síť** a vyberte **vytvořit novou** a nakonfigurujte novou virtuální síť.

    >[!div class="mx-imgBorder"]
    >![vytvořit novou virtuální síť pro nový virtuální počítač](./media/functions-create-private-site-access/create-vm-networking.png)

6. V části **vytvořit virtuální síť**použijte nastavení v tabulce pod obrázkem:

    >[!div class="mx-imgBorder"]
    >![vytvořit novou virtuální síť pro nový virtuální počítač](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Název** | myResourceGroup-vnet | Můžete použít výchozí název vygenerovaný pro vaši virtuální síť. |
    | **Rozsah adres** | 10.10.0.0/16 | Pro virtuální síť použijte jeden rozsah adres. |
    | **Název podsítě** | Kurz | Název podsítě. |
    | **Rozsah adres** (podsíť) | 10.10.1.0/24 | Velikost podsítě určuje, kolik rozhraní lze do podsítě přidat. Tuto podsíť používá virtuální počítač. `/24` podsíť poskytuje adresy hostitele 254. |

7. Vyberte **OK** a vytvořte virtuální síť.
8. Zpátky na kartě **sítě** zkontrolujte, jestli není vybraná **možnost žádná** pro **veřejnou IP adresu**.
9. Zvolte kartu **Správa** a potom v části **účet diagnostického úložiště**zvolte **vytvořit novou** a vytvořte nový účet úložiště.
10. Ponechte výchozí hodnoty pro oddíly **identity**, **automatického vypínání**a **zálohování** .
11. Vyberte **Zkontrolovat a vytvořit**. Po dokončení ověření vyberte **vytvořit**. Proces vytvoření virtuálního počítače trvá několik minut.

## <a name="configure-azure-bastion"></a>Konfigurace Azure bastionu

[Azure bastionu](https://azure.microsoft.com/services/azure-bastion/) je plně spravovaná služba Azure, která poskytuje zabezpečený přístup pomocí protokolu RDP a SSH k virtuálním počítačům přímo z Azure Portal. Použití služby Azure bastionu odebere nutnost konfigurovat nastavení sítě související s přístupem RDP.

1. Na portálu vyberte v horní části zobrazení skupiny prostředků možnost **Přidat** .
2. Do vyhledávacího pole zadejte "bastionu".  Vyberte "bastionu".
3. Výběrem **vytvořit** zahájíte proces vytváření nového prostředku Azure bastionu. V části **virtuální síť** se zobrazí chybová zpráva, protože ještě není `AzureBastionSubnet` podsíť. Podsíť se vytvoří v následujících krocích. Použijte nastavení v tabulce pod obrázkem:

    >[!div class="mx-imgBorder"]
    >![začátek vytváření bastionu Azure](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Název** | myBastion | Název nového prostředku bastionu |
    | **Oblast** | Střed USA – sever | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |
    | **Virtuální síť** | myResourceGroup-vnet | Virtuální síť, ve které bude prostředek bastionu vytvořen |
    | **Podsíť** | AzureBastionSubnet | Podsíť ve virtuální síti, do které bude nasazen nový prostředek hostitele bastionu. Podsíť je nutné vytvořit pomocí hodnoty `AzureBastionSubnet`název. Tato hodnota umožňuje službě Azure zjistit, do které podsítě nasadit prostředky bastionu. Je nutné použít minimálně `/27` nebo větší podsíť (`/27`, `/26`atd.). |

    > [!NOTE]
    > Podrobný návod, jak vytvořit prostředek Azure bastionu, najdete v kurzu [Vytvoření hostitele Azure bastionu](../bastion/bastion-create-host-portal.md) .

4. Vytvořte podsíť, ve které může Azure zřídit hostitele Azure bastionu. Zvolením **možnosti spravovat konfiguraci podsítě** otevřete nové podokno, kde můžete definovat novou podsíť.  Vyberte **+ podsíť** a vytvořte novou podsíť.
5. Podsíť musí mít název `AzureBastionSubnet` a předpona podsítě musí být aspoň `/27`.  Vyberte **OK** a vytvořte podsíť.

    >[!div class="mx-imgBorder"]
    >![vytvoření podsítě pro hostitele Azure bastionu](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. Na stránce **vytvořit bastionu** vyberte ze seznamu dostupných podsítí nově vytvořenou `AzureBastionSubnet`.

    >[!div class="mx-imgBorder"]
    >![vytvoření hostitele Azure bastionu s určitou podsítí](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Vyberte **zkontrolovat & vytvořit**. Po dokončení ověření vyberte **vytvořit**. Vytvoření prostředku Azure bastionu bude trvat několik minut.

## <a name="create-an-azure-functions-app"></a>Vytvoření aplikace Azure Functions

Dalším krokem je vytvoření aplikace Function App v Azure s využitím [plánu spotřeby](functions-scale.md#consumption-plan). Kód funkce se do tohoto prostředku nasadí později v tomto kurzu.

1. Na portálu vyberte v horní části zobrazení skupiny prostředků možnost **Přidat** .
2. Vyberte **compute > Function App**
3. V části **základy** použijte nastavení aplikace Function App, jak je uvedeno v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Skupina prostředků** | myResourceGroup | Vyberte skupinu prostředků, která bude obsahovat všechny prostředky pro tento kurz.  Použití stejné skupiny prostředků pro aplikaci Function App a virtuálního počítače usnadňuje vyčištění prostředků, pokud jste s tímto kurzem hotovi. |
    | **Název Function App** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou a – z (nerozlišuje velikost písmen), 0-9 a-. |
    | **Publikování** | Kód | Možnost publikování souborů kódu nebo kontejneru Docker. |
    | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. |
    | **Oblast** | Střed USA – sever | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |

    Vyberte tlačítko **Další: hostování >** .
4. V části **hostování** vyberte správný **účet úložiště**, **operační systém**a **Naplánujte** popis uvedený v následující tabulce.

    | Nastavení      | Navrhovaná hodnota  | Popis      |
    | ------------ | ---------------- | ---------------- |
    | **Účet úložiště** | Globálně jedinečný název | Vytvořte účet úložiště používaný vaší aplikací funkcí. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete použít i existující účet, který musí splňovat [požadavky na účet úložiště](./functions-scale.md#storage-account-requirements). |
    | **Operační systém** | Preferovaný operační systém | Operační systém je předem vybraný pro vás na základě výběru zásobníku modulu runtime, ale v případě potřeby můžete změnit nastavení. |
    | **Plánování** | Využití | [Plán hostování](./functions-scale.md) určuje, jak se aplikace funkcí škáluje, a prostředky dostupné pro jednotlivé instance. |
5. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte výběry konfigurace aplikace.
6. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

## <a name="configure-access-restrictions"></a>Konfigurace omezení přístupu

Dalším krokem je konfigurace [omezení přístupu](../app-service/app-service-ip-restrictions.md) , aby se zajistilo, že funkce může vyvolat jenom prostředky ve virtuální síti.

Přístup k [privátní lokalitě](functions-networking-options.md#private-site-access) je povolený vytvořením [koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) Azure Virtual Network mezi aplikací funkcí a zadanou virtuální sítí. Omezení přístupu jsou implementována prostřednictvím koncových bodů služby. Koncové body služeb zajišťují, že přístup k určenému prostředku má jenom přenosy pocházející z zadané virtuální sítě. V tomto případě je určeným prostředkem funkce Azure Functions.

1. V rámci aplikace Function App přejděte na kartu **funkce platformy** . Kliknutím na odkaz **síť** v hlavičce oddílu *sítě* otevřete oddíl stav síťové funkce.
2. **Stavová stránka funkce síť** je výchozím bodem ke konfiguraci front-bran Azure, Azure CDN a také omezení přístupu. Vyberte **konfigurovat omezení přístupu** pro konfiguraci přístupu k privátní lokalitě.
3. Na stránce **omezení přístupu** se zobrazí pouze výchozí omezení. Ve výchozím nastavení se neumísťují žádná omezení přístupu do aplikace Function App.  Vyberte **Přidat pravidlo** a vytvořte konfiguraci omezení přístupu k privátní lokalitě.
4. V podokně **Přidat omezení přístupu** vyberte v rozevíracím seznamu **typ** možnost **Virtual Network** a pak vyberte dříve vytvořenou virtuální síť a podsíť.
5. Na stránce **omezení přístupu** se teď zobrazuje nové omezení. Může trvat několik sekund, než se **stav koncového bodu** změní z `Disabled` až `Provisioning` na `Enabled`.

    >[!IMPORTANT]
    > Každá aplikace Function App obsahuje [Web pokročilého nástroje (Kudu)](../app-service/app-service-ip-restrictions.md#scm-site) , který se používá ke správě nasazení aplikací Function App. K tomuto webu se dostanete z adresy URL, jako je: `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Vzhledem k tomu, že v této lokalitě nasazení nejsou povolena omezení přístupu, můžete kód projektu nasadit z místní pracovní stanice nebo služby sestavení, aniž byste museli zřídit agenta v rámci virtuální sítě.

## <a name="access-the-functions-app"></a>Přístup k aplikaci Functions

1. Vraťte se do dříve vytvořené aplikace Function App.  V části **Přehled** zkopírujte adresu URL.

    >[!div class="mx-imgBorder"]
    >Adresa URL aplikace Function ![získat](./media/functions-create-private-site-access/access-function-overview.png)

2. Pokud se pokusíte získat přístup k aplikaci Function App hned z počítače mimo vaši virtuální síť, obdržíte stránku HTTP 403 s oznámením, že se aplikace zastaví.  Aplikace se nezastavila. Odpověď je ve skutečnosti stav zakázáno protokolu HTTP 403.
3. Nyní získáte přístup k funkci z dříve vytvořeného virtuálního počítače, který je připojen k vaší virtuální síti. Aby bylo možné získat přístup k webu z virtuálního počítače, budete se muset připojit k virtuálnímu počítači prostřednictvím služby Azure bastionu.  Nejdřív vyberte **připojit** a pak zvolte **bastionu**.
4. Zadejte požadované uživatelské jméno a heslo pro přihlášení k virtuálnímu počítači.  Vyberte **Connect** (Připojit). Otevře se nové okno prohlížeče, které vám umožní pracovat s virtuálním počítačem.
5. Vzhledem k tomu, že tento virtuální počítač přistupuje k funkci přes virtuální síť, je možné získat přístup k webu z webového prohlížeče na virtuálním počítači.  Je důležité si uvědomit, že zatímco aplikace Function App je dostupná jenom v rámci určené virtuální sítě, zůstane veřejná položka DNS. Jak je uvedeno výše, pokus o přístup k webu bude mít za následek odpověď HTTP 403.

## <a name="create-a-function"></a>Vytvoření funkce

Dalším krokem v tomto kurzu je vytvoření funkce Azure aktivované službou HTTP. Volání funkce přes HTTP GET nebo POST by mělo mít za následek odpověď "Hello, {Name}".  

1. Pomocí jednoho z následujících rychlých startů vytvořte a nasaďte aplikaci Azure Functions.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Příkazový řádek](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Když publikujete projekt Azure Functions, vyberte prostředek Function App, který jste vytvořili dříve v tomto kurzu.
3. Ověřte, že je funkce nasazená.

    >[!div class="mx-imgBorder"]
    >![nasazené funkce v seznamu funkcí](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Vyvolat funkci přímo

1. Aby bylo možné otestovat přístup k funkci, je nutné zkopírovat adresu URL funkce. Vyberte nasazenou funkci a pak vyberte **</> získat adresu URL funkce**. Potom kliknutím na tlačítko **Kopírovat** zkopírujte adresu URL do schránky.

    >[!div class="mx-imgBorder"]
    >![zkopírovat adresu URL funkce](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Když je funkce spuštěná, zobrazí se na portálu Chyba za běhu s oznámením, že modul runtime funkce nemůže být spuštěn. Navzdory textu zprávy je aplikace Function App skutečně spuštěná. Chyba je výsledkem nových omezení přístupu, která brání portálu v dotazování na kontrolu modulu runtime.

2. Vložte adresu URL do webového prohlížeče. Když se teď pokusíte o přístup k aplikaci Function App z počítače mimo vaši virtuální síť, dostanete odpověď HTTP 403, která indikuje, že je aplikace zastavená.

## <a name="invoke-the-function-from-the-virtual-network"></a>Vyvolat funkci z virtuální sítě

Přístup k funkci přes webový prohlížeč (pomocí služby Azure bastionu) na konfigurovaném VIRTUÁLNÍm počítači ve virtuální síti má za následek úspěch!

>[!div class="mx-imgBorder"]
>![přístup ke službě Azure Function prostřednictvím Azure bastionu](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky


> [!div class="nextstepaction"]
> [Další informace o možnostech sítě ve funkcích](./functions-networking-options.md)

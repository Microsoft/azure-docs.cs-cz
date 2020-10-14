---
title: Vytvoření testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Tento článek vás provede procesem vytvoření testovacího prostředí pomocí Azure Portal a Azure DevTest Labs.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9fa33a59dd35bfe3469f30f2349f8a08c45bd5e3
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058321"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Vytvoření testovacího prostředí v Azure DevTest Labs

Testovací prostředí ve službě Azure DevTest Labs je infrastruktura, která zahrnuje skupinu prostředků, třeba službu Virtual Machines, která vám umožní lépe spravovat tyto prostředky zadáním omezení a kvót. Tento článek vás provede procesem vytvoření testovacího prostředí pomocí webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

K vytvoření testovacího prostředí potřebujete:

* Předplatné Azure. Informace o možnostech nákupu Azure najdete v tématech [Jak koupit Azure](https://azure.microsoft.com/pricing/purchase-options/) nebo [Bezplatná zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/). Abyste mohli vytvořit testovací prostředí, musíte být vlastníky předplatného.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Začínáme s Azure DevTest Labs během minut]

Kliknutím na následující odkaz přejdete na stránku Azure Portal, která vám umožní začít vytvářet nové testovací prostředí v Azure DevTest Labs.

[Začínáme s Azure DevTest Labs během minut]](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Vyplnit nastavení pro nový účet

Na stránce **vytvořit DevTest Labs** vyplňte následující nastavení.

> [!TIP]
> V dolní části každé stránky najdete odkaz, který vám umožní **Stáhnout šablonu pro automatizaci**.

### <a name="basic-settings"></a>Základní nastavení

Ve výchozím nastavení se zobrazí karta **základní nastavení** . 

![základní nastavení](./media/devtest-lab-create-lab/basic-settings.png)

Vyplňte tyto hodnoty:

|Název|Popis|
|---|---|
|**Předplatné** | Povinná hodnota. Vyberte **Předplatné**, které bude přidruženo k testovacímu prostředí.|
|**Skupina prostředků**| Povinná hodnota. Zadejte **název skupiny prostředků** pro testovací prostředí. Pokud neexistuje, vytvořte ji.|
|**Název testovacího prostředí**| Povinná hodnota. Zadejte **název** testovacího prostředí.|
|**Umístění**|Povinná hodnota. Vyberte umístění, do kterého chcete testovací prostředí uložit.|
|**Veřejná prostředí**| Viz [Konfigurace a používání veřejných prostředí](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Nastavení automatického vypnutí

Přepněte na stránku **automatického vypínání** a zobrazte její nastavení. Automatické vypínání umožňuje automaticky vypnout všechny počítače v testovacím prostředí v každém dni naplánovaný čas.

![Karta automatického vypnutí](./media/devtest-lab-create-lab/auto-shutdown.png)

Na stránce můžete povolit **Automatické vypínání** a definování parametrů pro automatické vypínání všech virtuálních počítačů v testovacím prostředí. Funkce automatického vypnutí je především funkce na úsporu nákladů, pomocí které můžete určit, kdy se má virtuální počítač automaticky vypnout. Po vytvoření testovacího prostředí můžete nastavení automatického vypnutí změnit podle kroků uvedených v článku [Správa všech zásad pro testovací prostředí v Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

### <a name="networking"></a>Sítě

Při vytváření testovacího prostředí se vytvoří výchozí síť za vás. Přepněte na kartu **síť** , abyste změnili nebo nakonfigurovali nastavení podle potřeby. Vyberte například existující virtuální síť.

![Karta sítě ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>Značky

Pokud chcete vytvořit vlastní označování, které se přidá ke každému prostředku vytvořenému v testovacím prostředí, zadejte informace **NÁZEV** a **HODNOTA** pro **Značky**. Značky jsou užitečné a pomáhají při správě a organizaci prostředků testovacího prostředí podle kategorií. Další informace o značkách, včetně postupu pro přidání značek po vytvoření testovacího prostředí, najdete v tématu [Přidání značek do testovacího prostředí](devtest-lab-add-tag.md).

![Karta značky ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>Podokno Zkontrolovat a vytvořit

Po dokončení vyberte **vytvořit**. Stav procesu vytváření testovacího prostředí můžete sledovat v oblasti **oznámení** v pravém horním rohu stránky portálu. 

![Vytvořit kartu](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>Vytvoření bylo dokončeno.

Po dokončení se v dolní části stránky a v okně oznámení zobrazí tlačítko **Přejít na prostředek** . Případně aktualizujte stránku **DevTest Labs** , abyste viděli nově vytvořenou laboratoř v seznamu cvičení.  

![Vytvoření služby](./media/devtest-lab-create-lab/create-2.png)

Stiskněte tlačítko **Přejít k prostředku** a budete přesměrováni na domovskou stránku svého nového účtu DevTest Labs.

![Prostředek](./media/devtest-lab-create-lab/go-to-resource.png)

**DevTest Labs** můžete vyhledat také v Azure Portal. V seznamu vyberte nový účet a dostanete se na domovskou stránku. 

![Služba se vytvořila.](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>Další kroky

Po vytvoření testovacího prostředí je zde několik kroků, které je vhodné zvážit:

* [Zabezpečení přístupu k testovacímu prostředí](devtest-lab-add-devtest-user.md)
* [Nastavení zásad testovacího prostředí](devtest-lab-set-lab-policy.md)
* [Vytvoření šablony testovacího prostředí](devtest-lab-create-template.md)
* [Vytvoření vlastních artefaktů pro virtuální počítače](devtest-lab-artifact-author.md)
* [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)


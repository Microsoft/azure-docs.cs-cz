---
title: Vytvoření testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Tento článek vás provede procesem vytvoření testovacího prostředí pomocí Azure Portal a Azure DevTest Labs.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 962997bcc66188c66fd9db856fe44e4926f8e70c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019643"
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

Ve výchozím nastavení se zobrazí karta **základní nastavení** . Vyplňte tyto hodnoty:

|Název|Popis|
|---|---|
|**Předplatné** | Povinná hodnota. Vyberte **Předplatné**, které bude přidruženo k testovacímu prostředí.|
|**Skupina prostředků**| Povinná hodnota. Zadejte **název skupiny prostředků** pro testovací prostředí. Pokud neexistuje, vytvořte ji.|
|**Název testovacího prostředí**| Povinná hodnota. Zadejte **název** testovacího prostředí.|
|**Umístění**|Povinná hodnota. Vyberte umístění, do kterého chcete testovací prostředí uložit.|
|**Veřejná prostředí**| Viz [Konfigurace a používání veřejných prostředí](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Nastavení automatického vypnutí

Přepněte na stránku **automatického vypínání** a zobrazte její nastavení. Automatické vypínání umožňuje automaticky vypnout všechny počítače v testovacím prostředí v každém dni naplánovaný čas.

Na stránce můžete povolit **Automatické vypínání** a definování parametrů pro automatické vypínání všech virtuálních počítačů v testovacím prostředí. Funkce automatického vypnutí je především funkce na úsporu nákladů, pomocí které můžete určit, kdy se má virtuální počítač automaticky vypnout. Po vytvoření testovacího prostředí můžete nastavení automatického vypnutí změnit podle kroků uvedených v článku [Správa všech zásad pro testovací prostředí v Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

### <a name="networking"></a>Sítě

Při vytváření testovacího prostředí se vytvoří výchozí síť za vás.

Přepněte na kartu **síť** , abyste změnili nebo nakonfigurovali nastavení podle potřeby. Vyberte například existující virtuální síť.

### <a name="tags"></a>Značky

Pokud chcete vytvořit vlastní označování, které se přidá ke každému prostředku vytvořenému v testovacím prostředí, zadejte informace **NÁZEV** a **HODNOTA** pro **Značky**. Značky jsou užitečné a pomáhají při správě a organizaci prostředků testovacího prostředí podle kategorií. Další informace o značkách, včetně postupu pro přidání značek po vytvoření testovacího prostředí, najdete v tématu [Přidání značek do testovacího prostředí](devtest-lab-add-tag.md).

### <a name="review-and-create"></a>Podokno Zkontrolovat a vytvořit

Po dokončení vyberte **vytvořit**. Stav procesu vytváření testovacího prostředí můžete sledovat v oblasti **oznámení** v pravém horním rohu stránky portálu. 

## <a name="completed-the-creation"></a>Vytvoření bylo dokončeno.

Po dokončení se v dolní části stránky a v okně oznámení zobrazí tlačítko **Přejít na prostředek** . Případně aktualizujte stránku **DevTest Labs** , abyste viděli nově vytvořenou laboratoř v seznamu cvičení.  

Stiskněte tlačítko **Přejít k prostředku** a budete přesměrováni na domovskou stránku svého nového účtu DevTest Labs.

**DevTest Labs** můžete vyhledat také v Azure Portal. V seznamu vyberte nový účet a dostanete se na domovskou stránku. 

## <a name="next-steps"></a>Další kroky

Po vytvoření testovacího prostředí je zde několik kroků, které je vhodné zvážit:

* [Zabezpečení přístupu k testovacímu prostředí](devtest-lab-add-devtest-user.md)
* [Nastavení zásad testovacího prostředí](devtest-lab-set-lab-policy.md)
* [Vytvoření šablony testovacího prostředí](devtest-lab-create-template.md)
* [Vytvoření vlastních artefaktů pro virtuální počítače](devtest-lab-artifact-author.md)
* [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)


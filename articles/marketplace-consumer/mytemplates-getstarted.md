---
title: Začínáme se soukromými šablonami | Dokumentace Microsoftu
description: Přidávejte, spravujte a sdílejte svoje soukromé šablony pomocí portálu Azure, rozhraní příkazového řádku Azure nebo PowerShellu.
services: marketplace-customer
documentationcenter: ''
author: msmbaldwin
manager: asimm
editor: ''
tags: marketplace, azure-resource-manager
keywords: ''
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mbaldwin
ms.openlocfilehash: c716f54a1361d41dbad00e2e45562d5fbf8fd6ca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Začínáme se soukromými šablonami na webu Azure Portal
Šablona [Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) je deklarativní šablona, která slouží k definování nasazení. Můžete definovat, které prostředky se mají pro řešení nasadit, a určit parametry a proměnné, které vám umožní zadat hodnoty pro různá prostředí. Šablona se skládá z kódu JSON a z výrazů, které můžete použít k vytvoření hodnot pro vaše nasazení.

Můžete použít novou schopnost **Šablony** na webu [Azure Portal](https://portal.azure.com) spolu s poskytovatelem prostředků **Microsoft.Gallery** jako rozšíření [Azure Marketplace](https://azure.microsoft.com/marketplace/) a umožnit tak uživatelům vytváření, správu a nasazování soukromých šablon z osobní knihovny.

> [!NOTE]
> Místo používání soukromých šablon na portálu doporučuje Microsoft vytvořit aplikaci katalogu služeb prostřednictvím služby [Managed Applications](../managed-applications/overview.md). Aplikaci katalogu služeb můžete zpřístupnit uživatelům ve vaší organizaci.

Tento dokument vás provede přidáním, správou a sdílením soukromé **šablony** pomocí webu Azure Portal.

## <a name="guidance"></a>Doprovodné materiály
Následující návrhy vám pomůžou naplno využít výhody **šablon** při práci se svými řešeními:

* **Šablona** je zapouzdřující prostředek, který obsahuje šablonu Resource Manageru a další metadata. Chová se podobně jako položka v Marketplace. Klíčovým rozdílem je, že se jedná o soukromou položku, na rozdíl od veřejných položek Marketplace.
* Knihovna **Šablony** dobře funguje pro uživatele, kteří potřebují přizpůsobit svá nasazení.
* **Šablony** dobře fungují pro uživatele, kteří potřebují jednoduché úložiště v rámci Azure.
* Začněte s existující šablonou Resource Manageru. Najděte šablony na [GitHubu](https://github.com/Azure/azure-quickstart-templates) nebo [exportujte šablonu](../azure-resource-manager/resource-manager-export-template.md) z existující skupiny prostředků.
* **Šablony** jsou vázané na uživatele, který je publikuje. Název vydavatele je viditelný každému, kdo k němu má přístup pro čtení.
* **Šablony** jsou prostředky Resource Manageru a po publikování je nelze přejmenovat.

## <a name="add-a-template-resource"></a>Přidání prostředku šablony
Existují dva způsoby, jak lze na portálu Azure vytvořit prostředek **šablony**.

### <a name="method-1-create-a-new-template-resource-from-a-running-resource-group"></a>Způsob 1: Vytvoření nového prostředku šablony ze spuštěné skupiny prostředků
1. Na webu Azure Portal přejděte do existující skupiny prostředků. V **Nastavení** vyberte **Exportovat šablonu**.
2. Když je šablona Resource Manageru exportovaná, uložte ji pomocí tlačítka **Uložit šablonu** do úložiště **Šablony**. Podrobné informace o exportování šablony najdete [zde](../azure-resource-manager/resource-manager-export-template.md).
   <br /><br />
   ![Export skupiny prostředků](media/rg-export-portal1.PNG)
3. Vyberte příkazové tlačítko **Uložit do šablony**.
   <br /><br />
4. Zadejte následující informace:
   
   * Název – Název objektu šablony (Poznámka: Toto pole obsahuje název založený na Azure Resource Manageru. Platí všechna omezení pro pojmenovávání a název nelze po vytvoření změnit).
   * Popis – Stručný popis šablony.
     
     ![Uložení šablony](media/save-template-portal1.PNG)
5. Klikněte na **Uložit**.
   
   > [!NOTE]
   > Obsahuje-li exportovaná šablona Resource Manageru chyby, na portálu se zobrazí oznámení, ale stále budete moci tuto šablonu Resource Manageru uložit do Šablon. Před tím, než znovu nasadíte exportovanou šablonu Resource Manageru, se ujistěte, že jste zkontrolovali a opravili všechny problémy šablony Resource Manageru.
   > 
   > 

### <a name="method-2-add-a-new-template-resource-from-browse"></a>Způsob 2: Přidání nového prostředku šablony z procházení
Můžete také přidat novou **šablonu** od začátku pomocí příkazového tlačítka +Přidat v **Procházet > Šablony**. Zadejte Název, Popis a JSON šablony Resource Manageru.

![Přidání šablony](media/add-template-portal1.PNG)

> [!NOTE]
> Microsoft.Gallery je poskytovatel prostředků Azure umístěný v tenantovi. Prostředek šablony je vázán na uživatele, který ho vytvořil. Neváže se na konkrétní předplatné. Při nasazování šablony zvolte předplatné.
> 
> 

## <a name="view-template-resources"></a>Zobrazení prostředků šablony
Všechny **šablony**, které máte k dispozici, můžete zobrazit v **Procházet > Šablony**. Dostupné šablony zahrnují jak šablony, které jste vytvořili, tak i šablony, které jsou s vámi sdílené s různými úrovněmi oprávnění. Další informace najdete v tématu popisujícím [řízení přístupu](#access-control-for-a-tenant-resource-provider).

![Zobrazit šablonu](media/view-template-portal1.PNG)

Podrobnosti o **šabloně** zobrazíte kliknutím na položku v seznamu.

![Zobrazit šablonu](media/view-template-portal2c.png)

## <a name="edit-a-template-resource"></a>Úprava prostředku šablony
Tok upravování **šablony** můžete zahájit kliknutím pravým tlačítkem na položku v seznamu Procházet nebo zvolením příkazového tlačítka Upravit.

![Úprava šablony](media/edit-template-portal1a.PNG)

Můžete upravit popis nebo text šablony Resource Manageru. Název upravit nemůžete, protože se jedná o název prostředku Resource Manageru. Pokud upravíte kód JSON šablony Resource Manageru, ověříme ho, abychom zajistili, že jde o platný kód JSON. Vyberte **OK** a poté aktualizovanou šablonu uložte kliknutím na **Uložit**.

![Úprava šablony](media/edit-template-portal2a.PNG)

Po uložení šablony se zobrazí oznámení o potvrzení.

![Úprava šablony](media/edit-template-portal3b.png)

## <a name="deploy-a-template-resource"></a>Nasazení prostředku šablony
Můžete nasadit jakoukoli **šablonu**, ke které máte oprávnění ke **čtení**. Vyplňte hodnoty parametrů šablony Resource Manageru a pokračujte s nasazováním.

![Nasazení šablony](media/deploy-template-portal1b.png)

## <a name="share-a-template-resource"></a>Úprava prostředku šablony
Prostředek **Šablony** můžete sdílet s ostatními uživateli. Sdílení se chová podobně jako [přiřazování rolí pro jakýkoli prostředek na Azure](../role-based-access-control/role-assignments-portal.md). Vlastník **šablony** poskytuje oprávnění ostatním uživatelům, kteří mohou interagovat s prostředkem šablony. Osoba nebo skupina osob, se kterou **šablonu** sdílíte, uvidí šablonu Resource Manageru a její vlastnosti galerie.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Řízení přístupu pro prostředky Microsoft.Gallery
| Role | Oprávnění |
| --- | --- |
| Vlastník |Povoluje úplné řízení prostředku šablony včetně sdílení. |
| Čtenář |Povoluje čtení a spouštění (nasazování) prostředku šablony. |
| Přispěvatel |Povoluje upravování a právo odstranit prostředek šablony. Uživatel nemůže šablonu sdílet s ostatními. |

Kliknutím pravým tlačítkem nebo v zobrazení konkrétní položky vyberte na procházené položce **Sdílet**.

![Sdílení šablony](media/share-template-portal1a.png)

 Nyní můžete zvolit roli a uživatele nebo skupinu, kterým chcete poskytnout přístup ke konkrétní **šabloně**. Dostupné role jsou Vlastník, Čtenář a Přispěvatel.

![Sdílení šablony](media/share-template-portal2b.png)

![Sdílení šablony](media/share-template-portal3b.png)

Klikněte na **Vybrat** a **Ok**. Nyní vidíte uživatele a skupiny, které jste přidali k prostředku.

![Sdílení šablony](media/share-template-portal4b.png)

> [!NOTE]
> Šablonu je možné sdílet pouze s uživateli a skupinami ve stejném tenantu služby Azure Active Directory. Pokud šablonu sdílíte s e-mailovou adresou, která není ve vašem tenantovi, jejímu uživateli se odešle pozvánka s žádostí o připojení k tenantovi v roli hosta.
> 
> 

## <a name="next-steps"></a>Další kroky
* Další informace o vytváření šablon Resource Manageru naleznete v tématu [Vytváření šablon](../azure-resource-manager/resource-group-authoring-templates.md).
* Funkce, které můžete použít v šabloně Resource Manageru, jsou popsané v tématu [Funkce šablony](../azure-resource-manager/resource-group-template-functions.md).


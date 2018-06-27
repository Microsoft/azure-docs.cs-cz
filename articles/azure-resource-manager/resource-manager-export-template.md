---
title: Vyexportování šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Pomocí Azure Resource Manageru si můžete vyexportovat šablonu z existující skupiny prostředků.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 3e1dd8ad49ceb126a14070ed641146d91419640a
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025678"
---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Vyexportování šablony Azure Resource Manageru z existujících prostředků
V tomto článku se naučíte, jak exportovat šablonu Resource Manageru z existujících prostředků ve vašem předplatném. Tuto vygenerovanou šablonu můžete použít k lepšímu pochopení syntaxe šablon.

Existují dva způsoby, jak exportovat šablonu:

* Můžete exportovat **skutečnou šablonu použitou k nasazení**. Exportovaná šablona zahrnuje všechny parametry a proměnné přesně tak, jak jsou uvedeny v původní šabloně. Tento přístup je užitečný, pokud jste nasadili prostředky prostřednictvím portálu a chcete vidět šablonu, která tyto prostředky vytvoří. Tato šablona je ihned použitelná. 
* Můžete exportovat **vygenerovanou šablonu, která představuje aktuální stav skupiny prostředků**. Vyexportované šablony není založena na všechny šablony, který jste použili pro nasazení. Místo toho vytvoří šablonu, která je "snímek" nebo "zálohování" skupiny prostředků. Exportovaná šablona má řadu pevně definovaných hodnot a pravděpodobně méně parametrů, než byste obvykle definovali. Tuto možnost použijte k opětovnému nasazení prostředků do stejné skupiny prostředků. Chcete-li tuto šablonu použít pro jiné skupině prostředků, může mít to výrazně změnit.

Tento článek ukazuje obou přístupů prostřednictvím portálu.

## <a name="deploy-resources"></a>Nasazení prostředků
Začněme tím, že do Azure nasadíme prostředky, které můžete použít k exportu v podobě šablony. Pokud už v předplatném máte skupinu prostředků, kterou chcete exportovat do šablony, můžete tuto část přeskočit. Zbývající část tohoto článku předpokládá, že jste nasadili webovou aplikaci a SQL database řešení uvedené v této části. Pokud používáte jiné řešení, vaše prostředí může být trochu jiné, ale postup exportu šablony je stejný. 

1. V [portál Azure](https://portal.azure.com), vyberte **vytvořit prostředek**.
   
      ![Vyberte nový](./media/resource-manager-export-template/new.png)
2. Vyhledejte řešení **Webová aplikace a SQL** a vyberte ho z dostupných možností.
   
      ![Hledání webové aplikace a SQL](./media/resource-manager-export-template/webapp-sql.png)

3. Vyberte **Vytvořit**.

      ![Výběr možnosti vytvoření](./media/resource-manager-export-template/create.png)

4. Zadejte požadované hodnoty pro webovou aplikaci a databázi SQL. Vyberte **Vytvořit**.

      ![Zadejte web a hodnota SQL](./media/resource-manager-export-template/provide-web-values.png)

Nasazení může trvat minutu. Po dokončení nasazení vaše předplatné obsahuje řešení.

## <a name="view-template-from-deployment-history"></a>Zobrazení šablony z historie nasazení
1. Přejděte do skupiny prostředků pro nové skupiny prostředků. Všimněte si, že na portálu zobrazí výsledek posledního nasazení. Vyberte tento odkaz.
   
      ![Skupina prostředků](./media/resource-manager-export-template/select-deployment.png)
2. Zobrazí se vám historie nasazení pro skupinu. Ve vašem případě portálu pravděpodobně uvádí jenom jedno nasazení. Vyberte ho.
   
     ![Poslední nasazení](./media/resource-manager-export-template/select-history.png)
3. Na portálu zobrazí shrnutí nasazení. Souhrn obsahuje stav nasazení a jeho operací a také hodnoty, které jste zadali pro parametry. Pokud chcete zobrazit šablonu, kterou jste použili k nasazení, vyberte možnost **Zobrazit šablonu**.
   
     ![Zobrazení souhrnu nasazení](./media/resource-manager-export-template/view-template.png)
4. Resource Manager pro vás načte následujících sedm souborů:
   
   1. **Template** - Šablona, která definuje infrastrukturu pro vaše řešení. Když jste prostřednictvím portálu vytvářeli účet úložiště, Resource Manager k jeho nasazení použil šablonu a tuto šablonu uložil pro budoucí použití.
   2. **Parameters** - Soubor s parametry, který slouží k předávání hodnot během nasazení. Obsahuje hodnoty, které jste zadali při prvním nasazení. Kteroukoli z těchto hodnot můžete při opětovném nasazování šablony změnit.
   3. **Rozhraní příkazového řádku** -souboru skriptu rozhraní příkazového řádku Azure, který můžete použít k nasazení šablony.
   4. **PowerShell** - Soubor skriptu Azure PowerShellu, který můžete použít k nasazení šablony.
   5. **.NET** - Třída .NET, kterou můžete použít k nasazení šablony.
   6. **Ruby** - Třída Ruby, kterou můžete použít k nasazení šablony.
      
      Ve výchozím nastavení zobrazí na portálu šablonu.
      
       ![Zobrazit šablonu](./media/resource-manager-export-template/see-template.png)
      
Toto je skutečná šablona použitá k vytvoření vaší webové aplikace a databáze SQL. Všimněte si, že obsahuje parametry, které vám umožňují během nasazení zadat jiné hodnoty. Další informace o struktuře šablon najdete v tématu o [vytváření šablon Azure Resource Manageru](resource-group-authoring-templates.md).

## <a name="export-the-template-from-resource-group"></a>Export šablony ze skupiny prostředků
Pokud jste ručně změnili vašich prostředků nebo přidat prostředky do více nasazení, není načítání šablonu z historie nasazení odráží aktuální stav skupiny prostředků. V této části se dozvíte, jak exportovat šablonu, která odráží aktuální stav skupiny prostředků. Je určený jako snímek skupiny prostředků, který můžete použít k opětovnému nasazení do stejné skupiny prostředků. Pokud chcete použít pro jiná řešení vyexportované šablony, je třeba ji upravit výrazně.

> [!NOTE]
> Nelze exportovat šablonu pro skupinu prostředků, která má více než 200 prostředky.
> 
> 

1. Pokud chcete zobrazit šablonu pro skupinu prostředků, vyberte **Skript automatizace**.
   
      ![Export skupiny prostředků](./media/resource-manager-export-template/select-automation.png)
   
     Resource Manager vyhodnotí prostředky ve skupině prostředků a vygeneruje pro tyto prostředky šablonu. Ne všechny typy prostředků podporují funkci exportu šablony. Může se zobrazit chyba oznamující, že došlo k problému s exportem. Postup řešení těchto problémů najdete v části [Oprava problémů s exportem](#fix-export-issues).
2. Znovu se zobrazí šest souborů, které můžete použít k opětovnému nasazení řešení. Tentokrát je však šablona trochu jiná. Všimněte si, že vygenerovaná šablona obsahuje méně parametrů než šablona v předchozí části. Navíc je v této šabloně mnoho hodnot (jako hodnoty umístění a skladové položky) pevně zakódovaných místo toho, aby přijímaly hodnotu parametru. Před opětovným použitím této šablony možná budete chtít šablonu upravit, aby lépe využívala parametry. 
   
3. Máte pár možností, jak s touto šablonou dále pracovat. Šablonu si můžete stáhnout a pracovat na ní místně v editoru JSON nebo si ji můžete uložit do knihovny a pracovat s ní prostřednictvím portálu.
   
     Pokud vám vyhovuje, pomocí editoru JSON jako [VS Code](https://code.visualstudio.com/) nebo [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), můžete preferovat stahování šablony místně a pomocí tohoto editoru. Pokud chcete pracovat místně, vyberte **Stáhnout**.
   
      ![Stažení šablony](./media/resource-manager-export-template/download-template.png)
   
     Pokud nemáte nastavení v editoru JSON, možná budete chtít úpravě šablony prostřednictvím portálu. Zbývající část tohoto článku předpokládá, že jste uložili jste šablonu do knihovny na portálu. Stejné změny syntaxe ale můžete v šabloně provést, ať pracujete místně v editoru JSON nebo prostřednictvím portálu. Pokud chcete pracovat přes portál, vyberte **Přidat do knihovny**.
   
      ![Přidat do knihovny](./media/resource-manager-export-template/add-to-library.png)
   
     Při přidávání šablonu do knihovny, dáváte šablonu název a popis. Potom vyberte **Uložit**.
   
     ![Nastavené hodnoty šablony](./media/resource-manager-export-template/save-library-template.png)
4. Pokud chcete šablonu uloženou v knihovně zobrazit, vyberte **Další služby**, napište **Šablony**, abyste vyfiltrovali výsledky, a vyberte **Šablony**.
   
      ![Najít šablony](./media/resource-manager-export-template/find-templates.png)
5. Vyberte šablonu s názvem, který jste uložili.
   
      ![Vyberte šablonu](./media/resource-manager-export-template/select-saved-template.png)

## <a name="customize-the-template"></a>Přizpůsobení šablony
Vyexportovaná šablona funguje správně, pokud chcete vytvořit stejnou webovou aplikaci a databázi SQL pro všechna nasazení. Resource Manager ale nabízí možnosti pro ještě flexibilnější nasazení šablon. V tomto článku zjistíte, jak přidat parametry pro jméno a heslo správce databáze. Stejný postup můžete použít k přidání větší flexibility pro ostatní hodnoty v šabloně.

1. Vyberte **Upravit** pro přizpůsobení šablony.
   
     ![Zobrazit šablonu](./media/resource-manager-export-template/select-edit.png)
2. Vyberte šablonu.
   
     ![Upravit šablonu](./media/resource-manager-export-template/select-added-template.png)
3. Chcete-li předat hodnoty, které můžete chtít zadat během nasazování, přidejte následující dva parametry, které chcete **parametry** oddílu v šabloně:

   ```json
   "administratorLogin": {
       "type": "String"
   },
   "administratorLoginPassword": {
       "type": "SecureString"
   },
   ```

4. Pokud chcete použít nové parametry, nahraďte definici SQL serveru v části **resources**. Všimněte si, že **administratorLogin** a **administratorLoginPassword** jsou teď hodnoty parametrů.

   ```json
   {
       "comments": "Generalized from resource: '/subscriptions/{subscription-id}/resourceGroups/exportsite/providers/Microsoft.Sql/servers/tfserverexport'.",
       "type": "Microsoft.Sql/servers",
       "kind": "v12.0",
       "name": "[parameters('servers_tfserverexport_name')]",
       "apiVersion": "2014-04-01-preview",
       "location": "South Central US",
       "scale": null,
       "properties": {
           "administratorLogin": "[parameters('administratorLogin')]",
           "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
           "version": "12.0"
       },
       "dependsOn": []
   },
   ```

6. Vyberte **OK** po dokončení úprav šablony.
7. Uložte změny šablony kliknutím na **Uložit**.
   
     ![Uložení šablony](./media/resource-manager-export-template/save-template.png)
8. Pokud chcete aktualizovanou šablonu znovu nasadit, vyberte **Nasadit**.
   
     ![Nasazení šablony](./media/resource-manager-export-template/redeploy-template.png)
9. Zadejte hodnoty parametrů a vyberte skupinu prostředků, do které prostředky nasadíte.


## <a name="fix-export-issues"></a>Oprava problémů s exportem
Ne všechny typy prostředků podporují funkci exportu šablony. Zobrazí jenom tehdy, export problémy při exportu ze skupiny prostředků spíše než z historii nasazení. Pokud vaše poslední nasazení přesně reprezentuje aktuální stav skupiny prostředků, měli byste šablonu exportovat z historie nasazení, ne ze skupiny zdrojů. Exportujte pouze ze skupiny prostředků po provedení změny do skupiny prostředků, které nejsou definovány v jediné šabloně.

K vyřešení problémů s export ručně přidejte chybějící prostředky, které zpět do šablony. Chybová zpráva obsahuje typy prostředků, které nelze exportovat. Vyhledejte tyto typy prostředků v [referenčních informacích k šablonám](/azure/templates/). Pokud například chcete ručně přidat bránu virtuální sítě, přečtěte si [referenční informace k šablonám o prostředku Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways). Odkaz na šablonu vám dává JSON pro daný prostředek přidejte do šablony.

Po získání formátu JSON pro prostředek, potřebujete získat hodnoty prostředků. Hodnoty prostředku můžete zobrazit pomocí operaci GET v rozhraní REST API pro typ prostředku. Například pro získání hodnoty pro bránu virtuální sítě, najdete v části [získat brány virtuální sítě -](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="next-steps"></a>Další postup

* Šablonu můžete nasadit pomocí těchto možností: [PowerShell](resource-group-template-deploy.md), [Azure CLI](resource-group-template-deploy-cli.md) nebo [REST API](resource-group-template-deploy-rest.md).
* Tom, jak vyexportovat šablonu prostřednictvím Powershellu, najdete v sekci [šablon exportovat Azure Resource Manageru pomocí prostředí PowerShell](resource-manager-export-template-powershell.md).
* Tom, jak vyexportovat šablonu prostřednictvím rozhraní příkazového řádku Azure najdete v sekci [šablony exportovat Azure Resource Manager pomocí rozhraní příkazového řádku Azure](resource-manager-export-template-cli.md).


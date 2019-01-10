---
title: Datový model úroveň kompatibility ve službě Azure Analysis Services | Dokumentace Microsoftu
description: Princip úrovně kompatibility modelu tabulková data.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 31ca6deef6d81ca7beb08f6df1a15d52ef381a46
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190387"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Úroveň kompatibility pro tabulkové modely služby Analysis Services

*Úroveň kompatibility* odkazuje na konkrétní verzi chování v modulu služby Analysis Services. Změny úrovně kompatibility se obvykle shoduje s hlavní verze systému SQL Server. Tyto změny jsou také implementováno ve službě Azure Analysis Services udržovat rozdíly mezi obě platformy. Úroveň kompatibility se změní také vliv funkcí dostupných ve vašich tabulkových modelů. DirectQuery a metadat pro tabulkový objektový mít například jedná o rozdílné implementace v závislosti na úrovni kompatibility. Úroveň kompatibility se zadává v projektu s tabelárním modelem v aplikaci Visual Studio (SSDT). Tabulkové modely vytvořené v a importovat z Power BI Desktopu jsou na úrovni kompatibility 1400 pouze.

Služba Azure Analysis Services podporuje tabulkové modely na úrovni kompatibility 1200 a 1400. 

> [!NOTE]
> Power BI Desktopu. září 2018 a novější verze mají úroveň kompatibility .pbix 1465. Tato úroveň kompatibility se podporuje ve službě Azure Analysis Services. Ale import souboru Power BI Desktopu se nedoporučuje pro produkční prostředí. Další informace najdete v tématu [Import souboru Power BI Desktopu](analysis-services-import-pbix.md).

Je nejnovější úroveň kompatibility 1400. Tato úroveň se shoduje s SQL Server 2017 Analysis Services. Mezi hlavní funkce na úrovni kompatibility 1400 patří:

*  Nové funkce pro datové připojení a import s podporou pro vlastní rozhraní API a skriptování TMSL. 
*  Transformace dat a možnosti dat hybridní webové aplikace pomocí získat Data a M výrazů.
*  Opatření podporují řádky podrobností vlastnost výrazu jazyka DAX. Tato vlastnost umožňuje klientských nástrojů, jako je Microsoft Excel k podrobnostem a podrobná data ze souhrnné sestavy. Když uživatelé zobrazit celkový objem prodeje pro oblasti a měsíc, mohou prohlížet podrobnosti související objednávky. 
*  Zabezpečení na úrovni objektu pro názvy tabulek a sloupců, kromě dat v nich.
*  Vylepšená podpora pro nepravidelné hierarchie.
*  Sledování výkonu a vylepšení.
 
## <a name="set-compatibility-level"></a>Úroveň kompatibility sady

 Při vytváření nového projektu s tabelárním modelem v sadě SSDT, můžete zadat úroveň kompatibility na **Návrhář tabulkových modelů** dialogového okna. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Pokud vyberete **tuto zprávu znovu nezobrazovat** možnost, všechny následující projekty používají úroveň kompatibility, který jste zadali jako výchozí. Můžete změnit výchozí úroveň kompatibility v SSDT v **nástroje** > **možnosti**.  
  
 Chcete-li upgradovat existující projekt tabelárních modelů v SSDT, nastavte **úroveň kompatibility** vlastnost v modelu **vlastnosti** okna. Můžete se spolehnout, upgrade úroveň kompatibility je nevratná operace.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Zkontrolovat úroveň kompatibility pro databázi tabulkového modelu v aplikaci SQL Server Management Studio 

 V aplikaci SSMS klikněte pravým tlačítkem na název databáze > **vlastnosti** > **úroveň kompatibility**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Zkontrolujte podporované úroveň kompatibility pro server v aplikaci SSMS  

 V aplikaci SSMS klikněte pravým tlačítkem na název serveru > **vlastnosti** > **podporované úrovně kompatibility**.  
  
 Tato vlastnost určuje nejvyšší úroveň kompatibility databáze, která se spustí na serveru (s výjimkou ve verzi preview). Nelze změnit úroveň kompatibility podporované.  

## <a name="next-steps"></a>Další postup

  [Vytvoření modelu na webu Azure portal](analysis-services-create-model-portal.md)   
  [Správa služby Analysis Services](analysis-services-manage.md)  

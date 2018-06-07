---
title: 'Kurz služby Azure Analysis Services – Doplňková lekce: Dynamické zabezpečení | Dokumentace Microsoftu'
description: Popisuje, jak používat dynamické zabezpečení s využitím filtrů řádků v kurzu služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2a9a6c36863f7e30c803eecfe793c879d72eb249
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596936"
---
# <a name="supplemental-lesson---dynamic-security"></a>Doplňková lekce – Dynamické zabezpečení

V této doplňkové lekci vytvoříte další roli, která implementuje dynamické zabezpečení. Dynamické zabezpečení zajišťuje zabezpečení na úrovni řádků na základě uživatelského jména a ID přihlášení aktuálně přihlášeného uživatele. 
  
Za účelem implementace dynamického zabezpečení přidáte do svého modelu tabulku obsahující uživatelská jména uživatelů, kteří se můžou připojit k modelu a procházet objekty a data modelu. Model, který pomocí tohoto kurzu vytvoříte, je v kontextu Adventure Works. Pro absolvování tohoto kurzu však budete muset přidat tabulku obsahující uživatele z vaší vlastní domény. Pro přidaná uživatelská jména nepotřebujete hesla. K vytvoření tabulky EmployeeSecurity s malým vzorkem uživatelů z vaší vlastní domény použijete funkci Vložit, pomocí které vložíte data zaměstnanců z tabulky aplikace Excel. V reálném scénáři by tabulka obsahující uživatelská jména obvykle byla tabulka ze skutečné databáze jako zdroje dat, například skutečná tabulka DimEmployee.  
  
K implementaci dynamického zabezpečení použijete dvě funkce DAX: [funkci USERNAME (DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) a [funkci LOOKUPVALUE (DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab). Tyto funkce použité ve vzorci filtru řádků jsou definované v nové roli. Pomocí funkce LOOKUPVALUE vzorec určuje hodnotu z tabulky EmployeeSecurity. Vzorec pak tuto hodnotu předá do funkce USERNAME, která určí, jestli uživatelské jméno přihlášeného uživatele patří do této role. Uživatel pak může procházet pouze data určená filtrem řádků role. V tomto scénáři určíte, že zaměstnanci prodeje můžou procházet pouze data o internetovém prodeji pro prodejní oblasti, ve kterých jsou členem.  
  
Úkoly specifické pro tento scénář tabelárního modelu Adventure Works, které se ale nutně netýkají reálného scénáře, jsou jako takové označeny. Každý úkol obsahuje další informace s popisem účelu daného úkolu.  
  
Odhadovaný čas dokončení této lekce: **30 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma doplňkové lekce je součástí kurzu tabelárního modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této doplňkové lekci byste měli mít dokončeny všechny předchozí lekce.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>Přidání tabulky DimSalesTerritory do projektu s tabelárním modelem AW Internet Sales  
Pokud chcete implementovat dynamické zabezpečení pro tento scénář Adventure Works, musíte do modelu přidat další dvě tabulky. Jako první přidáte tabulku DimSalesTerritory (jako prodejní oblast) ze stejné databáze AdventureWorksDW. Potom na tabulku SalesTerritory použijete filtr řádků definující konkrétní data, která může přihlášený uživatel procházet.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>Přidání tabulky DimSalesTerritory  
  
1.  V části Průzkumník tabelárních modelů > **Zdroje Dat** klikněte pravým tlačítkem na vaše připojení a potom klikněte na **Importovat nové tabulky**.  

    Pokud se zobrazí dialogové okno Přihlašovací údaje k zosobnění, zadejte přihlašovací údaje k zosobnění, které jste použili v lekci 2: Přidání dat.
  
2.  V části Navigátor vyberte tabulku **DimSalesTerritory** a klikněte na **OK**.    
  
3.  V editoru dotazů klikněte na dotaz **DimSalesTerritory** a potom odeberte sloupec **SalesTerritoryAlternateKey**.  
  
7.  Klikněte na **Importovat**.  
  
    Nová tabulka se přidá do pracovního prostoru modelu. Objekty a data ze zdrojové tabulky DimSalesTerritory se následně naimportují do vašeho tabelárního modelu AW Internet Sales.  
  
9. Jakmile bude tabulka úspěšně importována, klikněte na **Zavřít**.  

## <a name="add-a-table-with-user-name-data"></a>Přidání tabulky s daty o uživatelských jménech  
Tabulka DimEmployee v ukázkové databázi AdventureWorksDW obsahuje uživatele z domény AdventureWorks. Tato uživatelská jména ve vašem prostředí neexistují. Musíte v modelu vytvořit tabulku, která bude obsahovat malý vzorek skutečných uživatelů (alespoň tři) z vaší organizace. Potom můžete tyto uživatele přidat jako členy do nové role. Pro ukázková uživatelská jména nepotřebujete hesla, potřebujete ale skutečná uživatelská jména systému Windows z vaší vlastní domény.  
  
#### <a name="to-add-an-employeesecurity-table"></a>Přidání tabulky EmployeeSecurity  
  
1.  Otevřete aplikaci Microsoft Excel a vytvořte list.  
  
2.  Zkopírujte následující tabulku, včetně řádku záhlaví, a vložte ji do listu.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  Nahraďte jméno, příjmení a doménu\uživatelské_jméno jménem a ID přihlášení tří uživatelů ve vaší organizaci. Do prvních dvou řádků (EmployeeId 1) vložte stejného uživatele, abyste určili, že tento uživatel patří k více než jedné prodejní oblasti. Pole EmployeeId a SalesTerritoryId ponechte tak, jak jsou.  
  
4.  Uložte list jako **SampleEmployee**.  
  
5.  V listu vyberte všechny buňky s daty o zaměstnancích, včetně záhlaví, klikněte na vybraná data pravým tlačítkem a potom klikněte na **Kopírovat**.  
  
6.  V SSDT klikněte na nabídku **Upravit** a potom na **Vložit**.  
  
    Pokud je možnost Vložit zobrazena šedě, klikněte na jakýkoli sloupec v libovolné tabulce v okně návrháře modelů a zkuste to znovu.  
  
7.  V dialogovém okně **Náhled vložení** do pole **Název tabulky** zadejte **EmployeeSecurity**.  
  
8.  V části **Vkládaná data** ověřte, že data zahrnují veškerá data o uživatelích a záhlaví z listu SampleEmployee.  
  
9. Ověřte, že je zaškrtnuté políčko **Použít první řádek jako záhlaví sloupců** a potom klikněte na **Ok**.  
  
    Vytvoří se nová tabulka EmployeeSecurity s daty o zaměstnancích zkopírovanými z listu SampleEmployee.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>Vytvoření relací mezi tabulkami FactInternetSales, DimGeography a DimSalesTerritory  
Tabulky FactInternetSales, DimGeography a DimSalesTerritory všechny obsahují společný sloupec SalesTerritoryId. Sloupec SalesTerritoryId v tabulce DimSalesTerritory obsahuje hodnoty s jiným ID pro každou prodejní oblast.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>Vytvoření relací mezi tabulkami FactInternetSales, DimGeography a DimSalesTerritory  
  
1.  V zobrazení diagramu v tabulce **DimGeography** klikněte a přidržte sloupec **SalesTerritoryId**, přesuňte kurzor na sloupec **SalesTerritoryId** v tabulce **DimSalesTerritory** a uvolněte tlačítko.  
  
2.  V tabulce **FactInternetSales** klikněte a přidržte sloupec **SalesTerritoryId**, přesuňte kurzor na sloupec **SalesTerritoryId** v tabulce **DimSalesTerritory** a uvolněte tlačítko.  
  
    Všimněte si, že vlastnost Aktivní pro tuto relaci má hodnotu False, což znamená, že je relace neaktivní. Tabulka FactInternetSales už má jinou aktivní relaci.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>Skrytí tabulky EmployeeSecurity před klientskými aplikacemi  
V tomto úkolu skryjete tabulku EmployeeSecurity a tím zabráníte jejímu zobrazování v seznamu polí klientských aplikací. Nezapomeňte, že skrytím tabulku nezabezpečíte. Uživatelé pořád můžou dotazovat data v tabulce EmployeeSecurity, pokud ví jak. Pro zabezpečení dat v tabulce EmployeeSecurity a zabránění uživatelům v jejich dotazování použijete v jednom z dalších úkolů filtr.  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>Skrytí tabulky EmployeeSecurity před klientskými aplikacemi  
  
-   V návrháři modelů klikněte pravým tlačítkem v zobrazení diagramu na záhlaví tabulky **Employee** a potom klikněte na **Skrýt před klientskými nástroji**.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Vytvoření role uživatele Zaměstnanci prodeje podle oblasti  
V tomto úkolu vytvoříte roli uživatele. Tato role zahrnuje filtr řádků definující, které řádky tabulky DimSalesTerritory jsou viditelné pro uživatele. Filtr se pak použije ve směru relace 1:N na všechny další tabulky související s tabulkou DimSalesTerritory. Použijete také filtr, který zabezpečí celou tabulku EmployeeSecurity před dotazy uživatelů, kteří jsou členy této role.  
  
> [!NOTE]  
> Role Zaměstnanci prodeje podle oblasti, kterou vytvoříte v této lekci, omezuje možnost uživatelů procházet (nebo dotazovat) data pouze na prodejní data pro prodejní oblast, ke které patří. Pokud do role Zaměstnanci prodeje podle oblasti přidáte uživatele, který je zároveň členem role vytvořené v [lekci 11: Vytvoření rolí](../tutorials/aas-lesson-11-create-roles.md), získáte kombinaci oprávnění. Pokud je uživatel členem více rolí, pak jsou oprávnění a filtry řádků definované pro každou roli kumulativní. To znamená, že uživatel má větší oprávnění daná kombinací rolí.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Vytvoření role uživatele Zaměstnanci prodeje podle oblasti  
  
1.  V SSDT klikněte na nabídku **Model** a potom na **Role**.  
  
2.  Ve **Správci rolí** klikněte na **Nový**.  
  
    Do seznamu se přidá nová role bez žádného oprávnění.  
  
3.  Klikněte na novou roli a potom ji ve sloupci **Název** přejmenujte na **Zaměstnanci prodeje podle oblasti**.  
  
4.  Ve sloupci **Oprávnění** klikněte na rozevírací seznam a vyberte oprávnění **Čtení**.  
  
5.  Klikněte na kartu **Členové** a pak na **Přidat**.  
  
6.  V dialogovém okně **Vybrat uživatele nebo skupinu** v části **Zadejte názvy objektů k výběru** zadejte první ukázkové uživatelské jméno, které jste použili při vytváření tabulky EmployeeSecurity. Kliknutím na **Zkontrolovat názvy** ověřte platnost uživatelského jména a potom klikněte na **Ok**.  
  
    Opakováním tohoto kroku přidejte další ukázková uživatelská jména, která jste použili při vytváření tabulky EmployeeSecurity.  
  
7.  Klikněte na kartu **Filtry řádků**.  
  
8.  Pro tabulku **EmployeeSecurity** zadejte do sloupce **Filtr DAX** následující vzorec:  
  
    ```
      =FALSE()  
    ```
  
    Tento vzorec určuje, že se všechny sloupce přeloží na logickou podmínku s hodnotou false. Členové role uživatele Zaměstnanci prodeje podle oblasti nemůžou dotazovat žádné sloupce tabulky EmployeeSecurity.  
  
9. Pro tabulku **DimSalesTerritory** zadejte následující vzorec:  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    V tomto vzorci funkce LOOKUPVALUE vrátí všechny hodnoty pro sloupec DimEmployeeSecurity[SalesTerritoryId], kde EmployeeSecurity[LoginId] je stejné jako uživatelské jméno systému Windows aktuálně přihlášeného uživatele a EmployeeSecurity[SalesTerritoryId] je stejné jako DimSalesTerritory[SalesTerritoryId].  
  
    Sada ID prodejních oblastí vrácená funkcí LOOKUPVALUE se pak použije k omezení zobrazených řádků v tabulce DimSalesTerritory. Zobrazí se pouze řádky, jejichž SalesTerritoryID se nachází v sadě ID vrácené funkcí LOOKUPVALUE.  
  
10. Ve Správci rolí klikněte na **Ok**.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Test role uživatele Zaměstnanci prodeje podle oblasti  
V tomto úkolu pomocí funkce Analýza v aplikaci Excel v SSDT otestujete efektivnost role uživatele Zaměstnanci prodeje podle oblasti. Zadáte jedno z uživatelských jmen, která jste přidali do tabulky EmployeeSecurity a jako člena této role. Toto uživatelské jméno se pak použije jako efektivní uživatelské jméno ve vytvořeném propojení mezi aplikací Excel a modelem.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>Test role uživatele Zaměstnanci prodeje podle oblasti  
  
1.  V SSDT klikněte na nabídku **Model** a potom na **Analyzovat v aplikaci Excel**.  
  
2.  V dialogovém okně **Analýza v aplikaci Excel** v části **Zadejte uživatelské jméno nebo roli pro připojení k modelu** vyberte **Jiný uživatel systému Windows** a potom klikněte na **Procházet**.  
  
3.  V dialogovém okně **Vybrat uživatele nebo skupinu** v části **Zadejte název objektu k výběru** zadejte uživatelské jméno, které jste zahrnuli do tabulky EmployeeSecurity, a potom klikněte na **Zkontrolovat názvy**.  
  
4.  Kliknutím na **Ok** zavřete dialogové okno **Vybrat uživatele nebo skupinu** a potom kliknutím na **Ok** zavřete dialogové okno **Analýza v aplikaci Excel**.  
  
    Otevře se aplikace Excel s novým sešitem. Automaticky se vytvoří kontingenční tabulka. Seznam polí kontingenční tabulky zahrnuje většinu datových polí, která jsou v novém modelu k dispozici.  
  
    Všimněte si, že tabulka EmployeeSecurity se v seznamu polí kontingenční tabulky nezobrazuje. Tuto tabulku jste skryli před klientskými nástroji v jednom z předchozích úkolů.  
  
5.  V seznamu **Pole** v části **∑ Internet Sales** (míry) vyberte míru **InternetTotalSales**. Tato míra se zadá do polí **Hodnoty**.  
  
6.  Vyberte sloupec **SalesTerritoryId** z tabulky **DimSalesTerritory**. Tento sloupec se zadá do polí **Popisky řádků**.  
  
    Všimněte si, že hodnoty internetového prodeje se zobrazí pouze pro jednu oblast, do které patří efektivní uživatelské jméno, které jste použili. Pokud jako pole popisku řádku vyberete jiný sloupec, třeba City z tabulky DimGeography, zobrazí se pouze města v prodejní oblasti, ke které patří efektivní uživatel.  
  
    Tento uživatel nemůže procházet ani dotazovat žádná data o internetovém prodeji pro jiné oblasti, než do kterých patří. Důvodem tohoto omezení je, že filtr řádků definovaný pro tabulku DimSalesTerritory v roli uživatele Zaměstnanci prodeje podle oblasti chrání veškerá data související s jinými prodejními oblastmi.  
  
## <a name="see-also"></a>Viz také  
[Funkce USERNAME (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[Funkce LOOKUPVALUE (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[Funkce CUSTOMDATA (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  
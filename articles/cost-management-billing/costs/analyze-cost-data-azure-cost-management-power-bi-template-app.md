---
title: Analýza nákladů na Azure s využitím aplikace Power BI
description: Tento článek vysvětluje, jak nainstalovat a používat aplikaci Power BI Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 1/29/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: d3439f5a86c445898d95191a32496b5f0c86be0b
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070153"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Analýza nákladů pomocí aplikace Power BI Azure Cost Management pro smlouvy Enterprise (EA)

Tento článek vysvětluje, jak nainstalovat a používat aplikaci Power BI Azure Cost Management. Aplikace vám pomůže analyzovat a spravovat náklady na Azure v Power BI. Pomocí aplikace můžete sledovat náklady, trendy využití a identifikovat možnosti optimalizace nákladů, abyste snížili výdaje.

Aplikace Azure Cost Management Power BI aktuálně podporuje pouze zákazníky s [smlouva Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

Aplikace omezuje její přidanou. Pokud chcete upravit a zvětšit výchozí filtry, zobrazení a vizualizace, abyste je mohli přizpůsobit vašim potřebám, použijte místo toho [Azure cost management konektor v Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management) . Pomocí konektoru Azure Cost Management se můžete připojit k dalším datům z jiných zdrojů a vytvořit přizpůsobené sestavy a získat tak holistický zobrazení vašich celkových obchodních nákladů. Konektor podporuje také smlouvy o zákaznících Microsoftu.

> [!NOTE]
> Aplikace s šablonou Power BI nepodporují stahování souboru PBIX.

## <a name="prerequisites"></a>Požadavky

- K instalaci a použití aplikace je potřeba [licence Power BI Pro](/power-bi/service-self-service-signup-for-power-bi).
- Pokud se chcete připojit k datům, musíte použít účet [správce Enterprise](../manage/understand-ea-roles.md). Podporuje se role podnikového správce (jenom pro čtení).

## <a name="installation-steps"></a>Kroky instalace

Postup instalace aplikace:

1. Otevřete [aplikaci Power BI Azure Cost Management](https://aka.ms/costmgmt/ACMApp).
2. Na stránce Power BI AppSource vyberte **Získat hned**.
3. Výběrem **Pokračovat** odsouhlasíte podmínky použití a zásady ochrany osobních údajů.
4. V poli **Instalovat tuto aplikaci Power BI** vyberte **Instalovat**.
5. V případě potřeby vytvořte pracovní prostor a vyberte **Pokračovat**.
6. Po dokončení instalace se zobrazí oznámení, že je vaše nová aplikace připravená.
7. Vyberte **Přejít do aplikace**.
8. V okně **Začínáme s novou aplikací** v části **Připojit data** vyberte **Připojit**.  
  ![Začínáme s novou aplikací – Připojit](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. V zobrazeném dialogovém okně zadejte do pole **BillingProfileIdOrEnrollmentNumber** číslo registrace EA. Zadejte počet měsíců, pro které se mají data získat. Ponechte výchozí hodnotu pro **Obor** na **Číslo registrace** a pak vyberte **Další**.  
  ![Zadání informací o registraci EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. Další dialogové okno se připojí k Azure a získá data požadovaná pro doporučení rezervovaných instancí. *Ponechte nakonfigurované výchozí hodnoty* a vyberte **Přihlásit se**.  
  ![Snímek obrazovky s dialogovým oknem Připojit k aplikaci Azure Cost Management a výchozími hodnotami](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. Poslední krok instalace se připojí k registraci EA a vyžaduje účet [podnikového správce](../manage/understand-ea-roles.md). Vyberte **Přihlásit** a ověřte se pomocí své registrace EA. Tento krok také v Power BI spustí akci aktualizace dat.  
  ![Připojení k registraci EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > Dokončení procesu aktualizace dat může trvat poměrně dlouho. Délka závisí na zadaném počtu měsíců a množství dat potřebných k synchronizaci.
12. Chcete-li zjistit stav aktualizace dat, vyberte v pracovním prostoru kartu **Datové sady**. Podívejte se vedle časového razítka Aktualizováno. Pokud pořád probíhá aktualizace, uvidíte příslušný ukazatel.  
  ![Aktualizace dat](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

Po dokončení aktualizace dat vyberte aplikaci Azure Cost Management, abyste zobrazili předem vytvořené sestavy.

## <a name="reports-available-with-the-app"></a>Sestavy dostupné v aplikaci

V aplikaci jsou k dispozici následující sestavy.

**Začínáme** – poskytuje užitečné odkazy na dokumentaci a odkazy k poskytnutí zpětné vazby.

**Přehled účtu** – sestava zobrazuje měsíční souhrn informací, jako například:

- Poplatky za kredity
- Nové nákupy
- Poplatky za Azure Marketplace
- Nadlimitní využití a celkové poplatky

**Využití podle předplatných a skupin prostředků** – poskytuje zobrazení nákladů v průběhu času a grafy zobrazující náklady podle předplatných a skupin prostředků.

**Využití podle služeb** – poskytuje zobrazení využití v průběhu času podle MeterCategory. Můžete sledovat data o využití a přejít k podrobnostem jakékoli anomálie, abyste porozuměli špičkám a propadům využití.

**5 hlavních ovladačů využití** – v sestavě se zobrazí souhrn vyfiltrovaných nákladů podle prvních 5 MeterCategory a odpovídajících MeterName.

**Využití AHB Windows Serveru** – sestava zobrazuje počet virtuálních počítačů, u kterých je povolené zvýhodněné hybridní využití Azure. Zobrazuje také počet jader nebo vCPU používaných virtuálními počítači.

![Úplná sestava zvýhodněného hybridního využití Azure](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

Tato sestava také identifikuje virtuální počítače s Windows, u kterých je **povolené** zvýhodněné hybridní využití, ale mají _méně než_ 8 vCPU. Zobrazuje také, kde zvýhodněné hybridní využití **není povolené** a přitom je k dispozici 8 _nebo více_ vCPU. Tyto informace vám pomůžou plně využívat zvýhodněné hybridní využití. Použijte výhodu na vaše nejdražší virtuální počítače a maximalizujte potenciální úspory.

![Zvýhodněné hybridní využití Azure – méně než 8 vCPU a nepovolené vCPU](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

**Vrácení peněz za rezervované instance** – sestava vám pomůže pochopit, kde a kolik výhod rezervované instance (RI) se používá pro oblast, předplatné, skupinu prostředků nebo prostředek. Sestava k zobrazení používá amortizovaná data o využití.

Abyste zobrazili data o nevyužití rezervovaných instancí, můžete použít filtr pro _chargetype_.

Další informace o amortizovaných datech najdete v článku [Získání nákladů na rezervace a jejich využití u smlouvy Enterprise](../reservations/understand-reserved-instance-usage-ea.md).

**Úspory za rezervované instance** – v sestavě se zobrazují úspory vyplývající z rezervací pro předplatné, skupinu prostředků a úroveň prostředků. Zobrazuje se:

- Náklady rezervace
- Odhadované náklady na vyžádání, pokud by se na použití nepoužila rezervace
- Úspora nákladů plynoucí z rezervace

 Sestava od celkových úspor odečte všechny marné náklady na nedostatečně využité rezervace. Bez rezervace by k marným nákladům nedošlo.

Amortizovaná data o využití můžete použít k sestavení dat.

<a name="shared-recommendation"></a>
**Pokrytí virtuálních počítačů pomocí RI (sdílené doporučení)** – sestava je rozdělená mezi využití virtuálních počítačů na vyžádání a využití virtuálních počítačů v rámci RI za vybrané období. Poskytuje doporučení pro nákupy RI pro virtuální počítače ve sdíleném rozsahu.

Chcete-li sestavu použít, vyberte filtr přechodu k podrobnostem.

![Sestava pokrytí virtuálních počítačů pomocí RI – výběr přechodu k podrobnostem](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

Vyberte oblast, kterou chcete analyzovat. Pak vyberte skupinu flexibility velikosti instance a tak dále.

Pro každou úroveň přechodu k podrobnostem se na sestavu použijí následující filtry:

- Data o pokrytí na pravé straně představují filtr, který ukazuje, kolik využití se účtuje podle sazeb na vyžádání a kolik je pokryto na základě rezervace.
- Doporučení se také filtrují.

Tabulka doporučení poskytuje doporučení pro nákup rezervací na základě používaných velikostí virtuálních počítačů.

Hodnoty _Normalizovaná velikost_ a _Doporučené množství normalizovaných_ vám pomůžou normalizovat nákup na nejmenší velikost pro skupinu flexibility velikosti instance. Tyto informace jsou užitečné, pokud plánujete zakoupit jenom jednu rezervaci pro všechny velikosti ve skupině flexibility velikosti instance.

![Doporučení RI](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

**Pokrytí virtuálních počítačů pomocí RI (jednotlivé doporučení)** – sestava je rozdělená mezi využití virtuálních počítačů na vyžádání a využití virtuálních počítačů v rámci RI za vybrané časové období. Poskytuje doporučení pro nákupy RI pro virtuální počítače v rozsahu předplatného.

Podrobnosti o tom, jak používat tuto sestavu, najdete v části [Pokrytí virtuálních počítačů pomocí RI (sdílené doporučení)](#shared-recommendation).

**Nákupy RI** – sestava zobrazuje nákupy rezervovaných instancí v zadaném období.

**Ceník** – sestava zobrazuje podrobný seznam cen specifických pro fakturační účet nebo registraci EA.

## <a name="troubleshoot-problems"></a>Poradce při potížích

Pokud máte problémy s aplikací Power BI, mohou vám při řešení potíží pomoci následující informace.

### <a name="error-processing-the-data-in-the-dataset"></a>Chyba při zpracování dat v datové sadě

Může se zobrazit chyba typu:

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

Místo `<TableName>` se zobrazí název tabulky.

#### <a name="cause"></a>Příčina

Výchozí hodnota **rozsahu** pro `Enrollment Number` se změnila v rámci připojení ke Cost Managementu.

#### <a name="solution"></a>Řešení

Znovu se připojte ke Cost Managementu a nastavte hodnotu **rozsahu** na `Enrollment Number`. Nezadávejte registrační číslo vaší organizace. Místo toho zadejte `Enrollment Number` přesně tak, jak se zobrazuje na následujícím obrázku.

![Zadání informací o registraci EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  

### <a name="budgetamount-error"></a>Chyba BudgetAmount

Může se zobrazit chyba typu:

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>Příčina

K této chybě dochází kvůli chybě základních metadat. K tomuto problému dochází, protože v části **Cost Management > Rozpočet** na webu Azure Portal není k dispozici žádný rozpočet. Oprava této chyby se průběžně nasazuje do Power BI Desktopu a služby Power BI. 

#### <a name="solution"></a>Řešení

- Do doby, než se tato chyba opraví, můžete problém obejít tak, že na webu Azure Portal na úrovni fakturačního účtu / registrace EA přidáte testovací rozpočet. Testovací rozpočet odblokuje připojení k Power BI. Další informace o vytvoření rozpočtu najdete v tématu [Kurz: Vytváření a správa rozpočtů Azure](tutorial-acm-create-budgets.md).


### <a name="invalid-credentials-for-azureblob-error"></a>Chyba typu Neplatné přihlašovací údaje pro AzureBlob

Může se zobrazit chyba typu:

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>Příčina

K této chybě dojde, pokud změníte metodu ověřování pro připojení objektu blob AutoFitComboMeter.

#### <a name="solution"></a>Řešení

1. Připojte se k datům.
1. Po zadání registrace EA a počtu měsíců zkontrolujte, že jste jako metodu ověřování ponechali výchozí hodnotu **Anonymní** a pro úroveň soukromí je nastavena hodnota **Žádné**.  
  ![Snímek obrazovky s dialogovým oknem Připojit k aplikaci Azure Cost Management a zadanými hodnotami Anonymní a Žádné](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png)  
1. Na další stránce jako metodu ověřování nastavte **OAuth2** a pro úroveň soukromí nastavte hodnotu **Žádné**. Potom se přihlaste a ověřte pomocí své registrace. Tento krok také v Power BI spustí aktualizace dat.


## <a name="data-reference"></a>Reference pro data

Následující informace shrnují data, která jsou k dispozici prostřednictvím aplikace. K dispozici jsou také odkazy na rozhraní API, která poskytují podrobné informace o datových polích a hodnotách.

| **Odkaz na tabulku** | **Popis** |
| --- | --- |
| **AutoFitComboMeter** | Data zahrnutá v aplikaci pro normalizaci doporučení RI a jejich použití na nejmenší velikost ve skupině rodiny instancí. |
| [**Shrnutí zůstatku**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Souhrn zůstatku pro smlouvy Enterprise. |
| [**Rozpočty**](/rest/api/consumption/budgets/get#definitions) | Podrobnosti o rozpočtu pro zobrazení skutečných nákladů nebo využití v rámci stávajících rozpočtových cílů. |
| [**Ceníky**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | Použitelné sazby měření pro zadaný fakturační profil nebo registraci EA. |
| [**Poplatky RI**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | Poplatky spojené s vašimi rezervovanými instancemi za posledních 24 měsíců. |
| [**Doporučení RI (sdílená)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | Doporučení pro nákup rezervovaných instancí založená na trendech využití všech vašich předplatných za posledních 7 dnů. |
| [**Doporučení RI (jednotlivé)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | Doporučení pro nákup rezervovaných instancí založená na trendech využití vašeho jednotlivého předplatného za posledních 7 dnů. |
| [**Podrobnosti využití RI**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | Podrobnosti o spotřebě pro stávající rezervované instance za poslední měsíc. |
| [**Souhrn využití RI**](/rest/api/consumption/reservationssummaries/list) | Procentní část denního využití rezervací Azure. |
| [**Podrobnosti využití**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Rozpis spotřebovaných množství a odhadovaných poplatků za daný fakturační profil v registraci EA. |
| [**Amortizované podrobnosti využití**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Rozpis spotřebovaných množství a odhadovaných amortizovaných poplatků za daný fakturační profil v registraci EA. |

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci dat, aktualizaci, sdílení sestav a dalším přizpůsobení sestav najdete v následujících článcích:

- [Konfigurace plánované aktualizace](/power-bi/refresh-scheduled-refresh)
- [Sdílení řídicích panelů a sestav Power BI se spolupracovníky a dalšími lidmi](/power-bi/service-share-dashboards)
- [Přihlášení vás a ostatních k sestavám a řídicím panelům ve službě Power BI](/power-bi/service-report-subscribe)
- [Stažení sestavy ze služby Power BI do Power BI Desktopu](/power-bi/service-export-to-pbix)
- [Uložení sestavy ve službě Power BI a Power BI Desktopu](/power-bi/service-report-save)
- [Vytvoření sestavy ve službě Power BI importem datové sady](/power-bi/service-report-create-new)
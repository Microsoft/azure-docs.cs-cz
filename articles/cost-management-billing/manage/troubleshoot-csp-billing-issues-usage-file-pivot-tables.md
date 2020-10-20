---
title: Řešení potíží s fakturací Azure CSP pomocí kontingenčních tabulek ze souborů s daty využití
description: Tento článek pomáhá řešit potíže s fakturací CSP (Azure Cloud Solution Provider) pomocí kontingenčních tabulek vytvořených z vašich souborů CSV s daty využití.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026384"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Řešení potíží s fakturací CSP pomocí kontingenčních tabulek ze souborů s daty využití

Tento článek pomáhá řešit potíže s fakturací CSP (Cloud Solution Provider) pomocí kontingenčních tabulek ve vašich souborech pro sesouhlasení (s daty využití) v Partnerském centru. Soubory s daty využití Azure obsahují veškeré informace o využití a spotřebě Azure. Informace v souboru vám pomůžou pomoct v těchto ohledech:

- Vysvětlení využití a uplatňování rezervací Azure
- Sesouhlasení informací v Azure Cost Management s vyúčtovanou fakturou
- Řešení potíží s náhlým zvýšením nákladů
- Vypočtení částky refundace u smlouvy o úrovni služeb

Na základě informací z vašich souborů s daty využití můžete získat lepší přehled o problémech s využitím a diagnostikovat je. Soubory s daty využití se generují ve formátu CSV. Vzhledem k tomu, že soubory CSV s daty využití můžou být poměrně velké, je práce s nimi jednodušší, když se zobrazí jako kontingenční tabulky v tabulkové aplikaci jako Excel. V příkladech v tomto článku se používá Excel, ale můžete použít i jakoukoli jinou tabulkovou aplikaci.

Přístup ke stažení souborů pro sesouhlasení mají jenom správci fakturace a globální správci. Další informace najdete v tématu věnovaném tomu, [jak číst řádkové položky v souborech pro sesouhlasení v Partnerském centru](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Získání dat a jejich naformátování

Vzhledem k tomu, že jsou soubory s daty využití Azure ve formátu CSV, je potřeba data nejdřív připravit na použití v Excelu. Pomocí následujících kroků naformátujte data jako tabulku.

1. Stáhněte si soubor s daty využití podle pokynů v části věnované tomu, jak [najít váš účet](/partner-center/read-your-bill#find-your-bill).
1. Otevřete daný soubor v Excelu.
1. Neformátovaná data se podobají následujícímu příkladu.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Vyberte první pole v tabulce, **PartnerID**.
1. Stisknutím kombinace kláves CTRL + SHIFT + šipka dolů a potom CTRL + SHIFT + šipka vpravo vyberte všechny informace v tabulce.
1. V horní nabídce vyberte **Vložení** > **Tabulka**. V dialogovém okně Vytvořit tabulku zaškrtněte políčko **Tabulka obsahuje záhlaví** a potom vyberte **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" :::
1. V horní nabídce vyberte **Vložení** > **Kontingenční tabulka** a potom vyberte **OK**. Tato akce vytvoří v souboru nový list a přesune vás do oblasti kontingenční tabulky na pravé straně listu.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

V oblasti Pole kontingenční tabulky je možné přetahovat položky myší. V další části se dozvíte, jak vytvořit kontingenční tabulku.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Vytvoření kontingenční tabulky pro zobrazení nákladů na Azure podle prostředků

V této části vytvoříte kontingenční tabulku, ve které můžete řešit obecné problémy s celkovým využitím Azure. V ukázkové tabulce můžete zkoumat, která služba spotřebovává nejvíc prostředků. Případně můžete zobrazit prostředky, které způsobují nejvyšší náklady, a podívat se, jak se určitá služba účtuje.

1. V oblasti Pole kontingenční tabulky přetáhněte pole **Service Name** a **Resource** do oblasti **Řádky**. Pole **Resource** umístěte pod pole **Service Name**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. V dalším kroku přetáhněte pole **Post-Tax Total** do pole **Hodnoty**. Můžete také místo toho použít sloupec Consumed Quantity a získat informace o jednotkách spotřeby a transakcích. Můžou to být například GB nebo hodiny. Místo nákladů zase můžete zobrazit transakce v různých měnách jako USD, EUR a INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Teď máte řídicí panel, na kterém můžete zkoumat obecné údaje o spotřebě. Pomocí možností filtrování v kontingenční tabulce můžete použít filtr na určitou službu.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Pokud chcete v kontingenční tabulce filtrovat druhou úroveň, například prostředek, vyberte v tabulce položku druhé úrovně.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Pokud potřebujete další filtry, můžete do oblasti **Filtry** přidat filtry **SubscriptionID** a **Customer Company Name** a vybrat požadovaný obor.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Vytvoření kontingenční tabulky pro zobrazení využití Azure podle data

V této části vytvoříte kontingenční tabulku, ve které můžete řešit obecné problémy s celkovým využitím Azure podle spotřebovaného množství a data. Může být užitečné zobrazit si špičky fakturace podle data a služby. Případně můžete zobrazit prostředky, které způsobují nejvyšší náklady, a podívat se, jak se určitá služba účtuje.

Soubor pro sesouhlasení obsahuje dvě tabulky. Jedna je v dokumentu nahoře (hlavní tabulka) a druhá dole. Druhá tabulka obsahuje řadu stejných informací jako ta první, ale nezahrnuje ceny ani podrobnosti o nákladech. Obsahuje ovšem datum využití a spotřebované množství.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Pomocí kroků uvedených v části [Získání dat a jejich naformátování](#get-the-data-and-format-it) vytvořte tabulku v Excelu s informacemi ze spodní části souboru pro sesouhlasení.
1. Když je tabulka připravená a máte list s kontingenční tabulkou, připravte řídicí panel pomocí kroků v Vytvoření kontingenční tabulky pro zobrazení nákladů na Azure podle prostředků. Místo použití sloupce Post-Tax Total přetáhněte do oblasti **Hodnoty** sloupec **Consumed quantity**.
1. Do oblasti Sloupce přidejte sloupec **Usage Date**. Kontingenční tabulka by měla vypadat zhruba jako v následujícím příkladu.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. Teď máte řídicí panel, který zobrazuje využití podle data. Každý měsíc můžete rozbalit výběrem symbolu **+** .

Na řídicím panelu se zobrazuje spotřebované množství v jednotkách, jako jsou GB, hodiny a přenosy.

Pokud chcete zobrazit cenu za den, můžete do oblasti **Řádky** přidat položku **Resource GUID**. Do horní tabulky přidejte jednotkovou cenu (**ListPrice**) daného prostředku. Vynásobte položku **ListPrice** položkou **Consumed quantity**. Výsledek odráží poplatky před zdaněním. Částky by se měly shodovat.

U některých prostředků (služeb) se za spotřebované množství účtují různě odstupňované ceny. Některé prostředky mají například vyšší cenu za prvních spotřebovaných 100 GB a nižší cenu za další využité GB. Při ručním výpočtu nákladů nezapomeňte odstupňované ceny zohlednit.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Vytvoření kontingenční tabulky pro zobrazení nákladů na určitý prostředek

U jednoho prostředku se může účtovat několik poplatků, protože ho využívají různé služby. Například u virtuálního počítače se můžou účtovat poplatky za výpočetní prostředek, licenci na operační systém, šířku pásma (přenosy dat), využití rezervovaných instancí a úložiště snímků. Kdykoli si budete chtít projít celkové využití konkrétních prostředků, následující kroky vás provedou vytvořením řídicího panelu ze souborů s daty využití, na kterém si zobrazíte celkové využití.

Soubory pro sesouhlasení neobsahují podrobnosti týkající se konkrétních prostředků. Proto použijete agregovaný soubor s daty využití. Kontaktujte [podporu fakturace Azure](https://go.microsoft.com/fwlink/?linkid=2083458) a požádejte o poskytnutí agregovaného souboru s daty využití pro vaše předplatné. Agregované soubory se generují na úrovni předplatného. Neformátovaná data se podobají následujícímu příkladu.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

Soubor obsahuje následující sloupce.

- **UsageStart** a **UsageEnd** – data pro jednotlivé řádkové položky (každá jednotka využití). Například každý den.
- **MeteredResourceID** – v Azure tento údaj odpovídá ID měřiče.
- **Properties** – obsahuje ID instance (název prostředku) s dalšími podrobnostmi, jako je například umístění.
- **Quantity** – odpovídá spotřebovanému množství v souboru pro sesouhlasení.

1. Vyberte první pole v tabulce, **PartnerID**.  
1. Stisknutím kombinace kláves CTRL + SHIFT + šipka dolů a potom CTRL + SHIFT + šipka vpravo vyberte všechny informace v tabulce.
1. V horní nabídce vyberte **Vložení** > **Tabulka**. V dialogovém okně Vytvořit tabulku zaškrtněte políčko **Tabulka obsahuje záhlaví** a potom vyberte **OK**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" :::
1. V horní nabídce vyberte **Vložení** > **Kontingenční tabulka** a potom vyberte **OK**. Tato akce vytvoří v souboru nový list a přesune vás do oblasti kontingenční tabulky na pravé straně listu.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Potom přidejte pole **MeteredResourceID** do oblasti **Řádky** a pole **Quantity** do oblasti **Hodnoty**. Výsledky zobrazí informace o celkovém využití. Pokud chcete zobrazit další podrobnosti, do oblasti **Sloupce** přetáhněte pole **UsageEndDateTime**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Příklad zobrazení neformátovaných dat v aplikaci Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Pokud chcete zobrazit souhrnnou sestavu, do oblasti **Řádky** přetáhněte pole **Properties** pod pole **MeteredResourceID**. Zobrazí se kompletní řídicí panel obsahující vaše využití.
1. Pokud chcete filtrovat podle konkrétního prostředku, do oblasti **Filtry** přidejte pole **Properties** a vyberte požadované využití. Název určitého prostředku můžete najít pomocí funkce vyhledávání.
    Pokud chcete zobrazit náklady na daný prostředek, vyhledejte celkové spotřebované množství a vynásobte tuto hodnotu ceníkovou cenou. Ceníková cena je pro každý identifikátor GUID prostředku (MeteredResourceID) jiná. Pokud prostředek spotřebovává několik měřičů (MeteredResourceID), musíte si poznamenat celkovou hodnotu pro každé ID.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Další kroky

- [Začínáme se službou Azure Cost Management pro partnery](../costs/get-started-partners.md)
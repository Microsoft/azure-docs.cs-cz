---
title: Řešení potíží s fakturací u smlouvy Azure MCA pomocí kontingenčních tabulek ze souborů s daty využití
description: Tento článek pomáhá řešit potíže s fakturací u smlouvy se zákazníkem Microsoftu (MCA) pomocí kontingenčních tabulek vytvořených z vašich souborů CSV s daty využití.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 32c0779f4086574aeaf8d38ea675f80fbd2c1ec7
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132223"
---
# <a name="troubleshoot-mca-billing-issues-with-usage-file-pivot-tables"></a>Řešení potíží s fakturací u smlouvy MCA pomocí kontingenčních tabulek ze souborů s daty využití

Tento článek pomáhá řešit potíže s fakturací u smlouvy se zákazníkem Microsoftu (MCA) pomocí kontingenčních tabulek z vašich souborů CSV s daty využití. Soubory s daty využití Azure obsahují veškeré informace o využití a spotřebě Azure. Informace v souboru vám pomůžou pomoct v těchto ohledech:

- Vysvětlení využití a uplatňování rezervací Azure
- Sesouhlasení informací v Azure Cost Management s vyúčtovanou fakturou
- Řešení potíží s náhlým zvýšením nákladů
- Vypočtení částky refundace u smlouvy o úrovni služeb

Na základě informací z vašich souborů s daty využití můžete získat lepší přehled o problémech s využitím a diagnostikovat je. Soubory s daty využití se generují ve formátu CSV. Vzhledem k tomu, že soubory CSV s daty využití můžou být poměrně velké, je práce s nimi jednodušší, když se zobrazí jako kontingenční tabulky v tabulkové aplikaci jako Excel. V příkladech v tomto článku se používá Excel, ale můžete použít i jakoukoli jinou tabulkovou aplikaci.

Ke stahování souborů s údaji o využití mají přístup jenom vlastníci fakturačního profilu, přispěvatelé, čtenáři nebo správci faktur. Podrobnosti najdete v tématu [Stažení informací o využití pro smlouvu se zákazníkem Microsoftu](./download-azure-invoice-daily-usage-date.md#download-usage-for-your-microsoft-customer-agreement). 

## <a name="get-the-data-and-format-it"></a>Získání dat a jejich naformátování

Vzhledem k tomu, že jsou soubory s daty využití Azure ve formátu CSV, je potřeba data nejdřív připravit na použití v Excelu. Pomocí následujících kroků naformátujte data jako tabulku.

1. Stáhněte si soubor s údaji o využití podle pokynů v tématu [Stažení využití na webu Azure Portal](./download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
1. Otevřete daný soubor v Excelu.
1. Neformátovaná data se podobají následujícímu příkladu.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/raw-csv-data-mca.png" :::
1. Vyberte první pole v tabulce, **invoiceID**.
1. Stisknutím kombinace kláves CTRL + SHIFT + šipka dolů a potom CTRL + SHIFT + šipka vpravo vyberte všechny informace v tabulce.
1. V horní nabídce vyberte **Vložení** > **Tabulka**. V dialogovém okně Vytvořit tabulku zaškrtněte políčko **Tabulka obsahuje záhlaví** a potom vyberte **OK**.  
:::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Příklad zobrazení neformátovaných dat" :::
1. V horní nabídce vyberte **Vložení** > **Kontingenční tabulka** a potom vyberte **OK**. Tato akce vytvoří v souboru nový list a přesune vás do oblasti kontingenční tabulky na pravé straně listu.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

V oblasti Pole kontingenční tabulky je možné přetahovat položky myší. V další části se dozvíte, jak vytvořit kontingenční tabulku.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Vytvoření kontingenční tabulky pro zobrazení nákladů na Azure podle prostředků

V této části vytvoříte kontingenční tabulku, ve které můžete řešit obecné problémy s celkovým využitím Azure. V ukázkové tabulce můžete zkoumat, která služba spotřebovává nejvíc prostředků. Případně můžete zobrazit prostředky, které způsobují nejvyšší náklady, a podívat se, jak se určitá služba účtuje.

1. V oblasti Pole kontingenční tabulky přetáhněte sloupce **Meter Category** a **Product** do části **Řádky**. Sloupec **Product** umístěte pod sloupec **Meter Category**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Potom přetáhněte sloupec **costInBillingCurrency** do části **Hodnoty**. Můžete také místo toho použít sloupec **Quantity** a získat informace o jednotkách spotřeby a transakcích. Můžou to být například GB nebo hodiny. Místo nákladů zase můžete zobrazit transakce v různých měnách jako USD, EUR a INR.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Teď máte řídicí panel, na kterém můžete zkoumat obecné údaje o spotřebě. Pomocí možností filtrování v kontingenční tabulce můžete použít filtr na určitou službu.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Pokud chcete v kontingenční tabulce filtrovat druhou úroveň, například prostředek, vyberte v tabulce položku druhé úrovně.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Přetáhněte sloupec **ResourceID** do oblasti **Řádky** pod sloupec **Product**, aby se zobrazily náklady na každou službu podle prostředků.
1. Pokud chcete zobrazit denní spotřebu pro daný produkt, přidejte do oblasti **Sloupce** sloupec **date**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Měsíce můžete rozbalit a sbalit pomocí symbolů **+** ve sloupcích měsíců.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::

Přidání sloupců **Cost** a **Quantity** do oblasti **Hodnoty** je volitelné. Pokud to uděláte, pod každým měsícem a dnem, kdy se v oblasti Sloupce kontingenční tabulky vyskytuje sloupec Date, se pro obě oblasti dat vytvoří dva sloupce.

Pokud chcete použít doplňující filtry, můžete do oblasti Filtry přidat sloupce InvoiceSection, costCenter, SubscriptionID, ResourceGroupName nebo Tags a vybrat požadovaný obor.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Vytvoření kontingenční tabulky pro zobrazení nákladů na určitý prostředek

U jednoho prostředku se může účtovat několik poplatků, protože ho využívají různé služby. Například u virtuálního počítače se můžou účtovat poplatky za výpočetní prostředek, licenci na operační systém, šířku pásma (přenosy dat), využití rezervovaných instancí a úložiště snímků. Kdykoli si budete chtít projít celkové využití konkrétních prostředků, následující kroky vás provedou vytvořením řídicího panelu ze souborů s daty využití, na kterém si zobrazíte celkové využití.

1. V nabídce kontingenční tabulky vpravo přetáhněte sloupec **ResourceID** do oblasti **Filtr**.
1. Vyberte prostředek, pro který chcete zobrazit náklady. Pokud chcete vyhledat název prostředku, zadejte ho do pole **Hledat**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/resource-id-search.png" :::
1. Do oblasti **Řádky** přidejte sloupce **meterCategory** a **Product**. Sloupec **Product** umístěte pod sloupec **meterCategory**.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Příklad zobrazení neformátovaných dat" lightbox="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Potom přetáhněte sloupec **Extended Cost** do části **Hodnoty**. Můžete také místo toho použít sloupec Consumed Quantity a získat informace o jednotkách spotřeby a transakcích. Můžou to být například GB nebo hodiny. Místo nákladů zase můžete zobrazit transakce v různých měnách jako USD, EUR a INR. Teď máte k dispozici řídicí panel zobrazující všechny služby, které daný prostředek spotřebovává.
1. Do oblasti **Sloupce** přidejte sloupec **Date**. Zobrazí se denní spotřeba.
1. Měsíce můžete rozbalit a sbalit pomocí symbolů **+** ve sloupcích jednotlivých měsíců.  
    :::image type="content" source="./media/troubleshoot-customer-agreement-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Příklad zobrazení neformátovaných dat" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Další kroky

- [Prozkoumání a analýza nákladů pomocí analýzy nákladů](../costs/quick-acm-cost-analysis.md)
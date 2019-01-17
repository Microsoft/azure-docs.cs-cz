---
title: Zobrazení protokolů aktivit Azure pro monitorování prostředků | Dokumentace Microsoftu
description: Použití protokolů aktivit do akce kontroly uživatelů a chyby. Zobrazuje portálového prostředí Azure PowerShell, rozhraní příkazového řádku Azure a REST.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 636e4d5216f87440463fbaecd7f6c7a5a25c7502
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359387"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Zobrazení protokolů aktivit pro auditování akcí u prostředků

Na základě protokolů aktivit můžete zjistit:

* jaké operace provedené na prostředky v rámci vašeho předplatného
* kdo operaci zahájil (i když operací iniciovaných back-end službu nevrátí uživatele jako volající)
* Při operaci došlo k chybě
* Stav operace
* Hodnoty dalších vlastností, které vám mohou pomoci při zkoumání operace

Protokol aktivit obsahuje všechny operace zápisu (PUT, POST, DELETE) prováděné s vašimi prostředky. Neobsahuje operace čtení (GET). Seznam prostředků akcí najdete v tématu [operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md). Protokoly auditu můžete použít k vyhledání chyby při odstraňování potíží nebo k monitorování, jak uživatel ve vaší organizaci změnil prostředek.

Protokoly aktivit se uchovávají 90 dnů. Můžete se dotazovat na libovolný rozsah kalendářních dat, pokud počáteční datum neleží více než 90 dnů v minulosti.

Můžete načíst informace z protokolů aktivit na portálu, Powershellu, rozhraní příkazového řádku Azure Insights REST API, nebo [knihovna .NET Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portál

1. Pokud chcete zobrazit protokoly aktivit na portálu, vyberte **monitorování**.

    ![Vyberte protokoly aktivit](./media/resource-group-audit/select-monitor.png)

   Nebo, pokud chcete automaticky filtrovat protokol aktivity pro určitý prostředek nebo skupinu prostředků, vyberte **protokolu aktivit**. Všimněte si, že v protokolu aktivit se automaticky filtruje podle vybraného prostředku.

    ![Filtrovat podle prostředků](./media/resource-group-audit/filtered-by-resource.png)
2. V **protokolu aktivit**, prohlédnout souhrnné informace o poslední operace.

    ![Zobrazit akce](./media/resource-group-audit/audit-summary.png)
3. Pokud chcete omezit počet operací zobrazí, vyberte různé podmínky. Například na následujícím obrázku **Timespan** a **zahájit událost** změnit pole, chcete-li zobrazit akce prováděné konkrétního uživatele nebo aplikace pro poslední měsíc. Vyberte **použít** Chcete-li zobrazit výsledky dotazu.

    ![nastavení filtru](./media/resource-group-audit/set-filter.png)

4. Pokud je potřeba znovu spustit dotaz, vyberte **připnout aktuální filtry** a pojmenujte ho.

    ![Uložit dotaz](./media/resource-group-audit/save-query.png)
5. K rychlému spouštění dotazu, můžete vybrat jednu z předdefinovaných dotazů, jako je například selhání nasazení.

    ![Výběr dotazu](./media/resource-group-audit/select-quick-query.png)

   Vybraný dotaz automaticky nastaví hodnoty požadovaný filtr.

    ![Zobrazit chyby nasazení](./media/resource-group-audit/view-failed-deployment.png)

6. Vyberte jednu z operací, které chcete zobrazit souhrn události.

    ![Operace zobrazení](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell

1. Pokud chcete načíst položky protokolu, spusťte **Get-AzureRmLog** příkazu. Další parametry pro filtrování seznamu položek, které zadáte. Pokud nezadáte čas zahájení a ukončení, budou vráceny položky za poslední hodinu. Například pokud chcete načíst operace pro skupinu prostředků za poslední hodinu spusťte:

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```

    Následující příklad ukazuje způsob použití protokolu aktivit do výzkumu operací provedených v určitou dobu. Počáteční a koncové datum jsou zadány ve formátu data.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Nebo datové funkce můžete zadat rozsah dat, jako je například posledních 14 dní.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. V závislosti na čas spuštění, který zadáte může vrátit předchozí příkazy dlouhý seznam operací pro skupinu prostředků. Můžete filtrovat výsledky pro co jste hledali zadáním kritérií hledání. Například pokud se pokoušíte Chcete-li zjistit, jak byla webová aplikace zastavena, můžete spustit následující příkaz:

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    V tomto příkladu zobrazí, že se provádí akce zastavení someone@contoso.com.

  ```powershell
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. Můžete vyhledat akce prováděné konkrétní uživatel, dokonce i pro skupinu prostředků, která již existuje.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Můžete filtrovat pro neúspěšné operace.

  ```azurepowershell-interactive
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Pohledem na stavové zprávy pro tuto položku se můžete soustředit na jednu chybu.

  ```azurepowershell-interactive
  ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
  ```

    Který vrátí:

        code           message
        ----           -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP.

## <a name="azure-cli"></a>Azure CLI

Pokud chcete načíst položky protokolu, spusťte [az monitor protokolu aktivit seznamu](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) příkazu.

  ```azurecli
  az monitor activity-log list --resource-group <group name>
  ```

## <a name="rest-api"></a>REST API

Operace REST pro práci s protokolu aktivit jsou součástí [rozhraní REST API služby Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx). Pokud chcete načíst události protokolu aktivit, naleznete v tématu [seznamu událostí správy v rámci předplatného](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Další postup

* Protokoly aktivit Azure je možné s Power BI a získejte větší přehled o akcích ve vašem předplatném. Zobrazit [zobrazení a analýza protokolů aktivit Azure v Power BI a další](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Další informace o nastavení zásad zabezpečení najdete v tématu [řízení přístupu na základě Role v Azure](../role-based-access-control/role-assignments-portal.md).
* Další informace o příkazech pro zobrazení operací nasazení, najdete v článku [zobrazení operací nasazení](resource-manager-deployment-operations.md).
* Naučte se, abyste zabránili odstranění prostředku pro všechny uživatele, najdete v článku [zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).
* Pokud chcete zobrazit seznam operací dostupných pro každého zprostředkovatele Microsoft Azure Resource Manageru, najdete v článku [operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md)

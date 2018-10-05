---
title: Tabulek v Azure | Dokumentace Microsoftu
description: Konfigurace správy potenciálních zákazníků pro Azure Table.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809538"
---
<a name="lead-management-instructions-for-azure-table"></a>Průběžná správa pokyny, jak tabulek v Azure
============================================

Tento článek popisuje, jak nakonfigurovat tabulku Azure pro ukládání potenciálních zákazníků. Tabulka Azure umožňuje ukládat a upravit informace o zákaznících.

## <a name="to-configure-azure-table"></a>Ke konfiguraci Azure Table

1.  Pokud nemáte účet Azure, můžete si [vytvořit Bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).

2.  Poté, co je aktivní účet Azure, přihlaste se k [webu Azure portal](https://portal.azure.com).
3.  Na webu Azure Portal vytvořte účet úložiště. Následující snímek obrazovky ukazuje, jak vytvořit účet úložiště. Další informace o cenách služby storage, najdete v části [ceny za službu storage](https://azure.microsoft.com/pricing/details/storage/).

    ![Postup vytvoření účtu služby Azure storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Zkopírujte připojovací řetězec účtu úložiště pro klíč a vložte ho do **připojovací řetězec účtu úložiště** na portál partnerů cloudu. Je například stingu připojení `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Klíč úložiště Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Můžete použít [Průzkumníka služby Azure storage](http://azurestorageexplorer.codeplex.com/) nebo jakýkoli jiný nástroj k zobrazení dat v tabulce úložiště. Můžete také exportovat data v tabulce Azure.
data.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(Volitelné)**  Službě Azure Functions s Azure table

Pokud chcete přizpůsobit, jak se zobrazuje potenciálních zákazníků, použijte [Azure Functions](https://azure.microsoft.com/services/functions/) s Azure table. Služba Azure Functions umožňuje automatizovat proces generování potenciálních zákazníků.

Následující kroky ukazují, jak vytvořit funkci Azure, který používá časovač. Každých pět minut funkce hledá v tabulce Azure pro nové záznamy a potom pomocí služby SendGrid k odesílání e-mailové oznámení.


1.  [Vytvoření](https://portal.azure.com/#create/SendGrid.SendGrid) bezplatný účet služby SendGrid ve vašem předplatném Azure.

    ![Vytvoření služby SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  Vytvořit klíč rozhraní API SendGrid 
    - Vyberte **spravovat** přechod k uživatelskému rozhraní služby SendGrid
    - Vyberte **nastavení**, **klíče rozhraní API**a pak vytvořte klíč, který se má odeslat e-mailu -\> úplný přístup
    - Uložit klíč rozhraní API


    ![Klíč rozhraní API SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  [Vytvoření](https://portal.azure.com/#create/Microsoft.FunctionApp) aplikaci Azure Function app pomocí možnost plánu hostování s názvem "Plánu Consumption".

    ![Vytvoření aplikace Azure Function App](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  Vytvořte novou definici funkce.

    ![Vytvoření definice funkce Azure functions](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  Chcete-li získat funkce pro odeslání aktualizace na určitou dobu, vyberte **TimerTrigger-CSharp** jako možnost spuštění.

     ![Azure možnost aktivace čas – funkce](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  Nahraďte kód "Vývoj" následujícím příkladu kódu. Upravte e-mailové adresy s adresami, které chcete použít pro odesílatele a příjemce.

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Fragment kódu Azure – funkce](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  Vyberte **integrace** a **vstupy** k definování připojení tabulky Azure.

    ![Integrace Azure – funkce](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  Zadejte název tabulky a definujte připojovací řetězec tak, že vyberete **nové**.


    ![Připojení tabulky Azure – funkce](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  Nyní definovat výstup jako SendGrid a zachovat všechny výchozí hodnoty.

    ![Výstup SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![Výchozí výstup SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Přidat klíč rozhraní API SendGrid do nastavení aplikace Function App pomocí názvu "SendGridApiKey" a hodnota získaná z klíče rozhraní API v uživatelském prostředí služby SendGrid

    ![Správa služby SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid spravovat klíče](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Po dokončení konfigurace funkce, kód v části Integrace by měl vypadat jako v následujícím příkladu.

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. Posledním krokem je přejděte na vývoj uživatelského rozhraní funkce a pak vyberte **spustit** ke spuštění časovače. Nyní pokaždé, když se nový zájemce je k dispozici ve, dostanete oznámení.

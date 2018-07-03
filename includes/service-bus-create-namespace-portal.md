---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 503a1db726483709c351ac9861c5d54913de55d3
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132003"
---
Pokud chcete začít používat entity zasílání zpráv služby Service Bus v Azure, musíte nejprve vytvořit obor názvů s jedinečným názvem v rámci Azure. Obor názvů poskytuje kontejner oboru pro adresování prostředků služby Service Bus v rámci vaší aplikace.

Vytvoření oboru názvů:

1. Přihlaste se na web [Azure Portal][Azure portal].
2. V levém navigačním podokně portálu klikněte na **+ Vytvořit prostředek**, potom klikněte na **Integrace** a pak na **Service Bus**.
3. V dialogovém okně **Vytvořit obor názvů** zadejte název oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.
4. Po kontrole, že je název oborů názvů k dispozici, zvolte cenovou úroveň (Basic, Standard nebo Premium).
5. V poli **Předplatné** zvolte předplatné Azure, ve které chcete vytvořit obor názvů.
6. V poli **Skupina prostředků** zvolte existující skupinu prostředků, ve které bude obor názvů fungovat, nebo vytvořte novou.      
7. V poli **Umístění**, vyberte zemi nebo oblast, ve které by měl být oboru názvů hostován.
   
    ![Vytvoření oboru názvů][create-namespace]
8. Klikněte na možnost **Vytvořit**. Systém teď vytvoří obor názvů a povolí ho. Pravděpodobně budete muset několik minut počkat, než systém zřídí prostředky pro váš účet.

### <a name="obtain-the-management-credentials"></a>Získání přihlašovacích údajů pro správu

Vytvořením nového oboru názvů se automaticky vygeneruje počáteční pravidlo sdíleného přístupového podpisu (SAS) s přidruženým párem primárního a sekundárního klíče, které udělují úplnou kontrolu nad všemi aspekty tohoto oboru názvů. V článku [Ověřování a autorizace Service Bus](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) najdete informace o vytvoření dalších pravidel s omezenějšími oprávněními pro běžné odesílatele a příjemce. Pokud chcete zkopírovat počáteční pravidlo, postupujte následovně: 

1. Klikněte na **Všechny prostředky** a pak klikněte na název nově vytvořeného oboru názvů.
2. V okně oboru názvů klikněte na **Zásady sdíleného přístupu**.
3. Na obrazovce **Zásady sdíleného přístupu** klikněte na **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. V okně **Zásada: RootManageSharedAccessKey** klikněte na tlačítko pro kopírování vedle položky **Připojovací řetězec – primární klíč** a zkopírujte připojovací řetězec do schránky k pozdějšímu použití. Vložte tuto hodnotu do Poznámkového bloku nebo jiného dočasného umístění.
   
    ![connection-string][connection-string]

5. Opakujte předchozí krok, zkopírujte si hodnotu pro **primární klíč** a vložte ji do dočasného umístění pro pozdější použití.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com

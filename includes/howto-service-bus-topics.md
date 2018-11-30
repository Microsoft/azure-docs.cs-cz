---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: ef6d5d22f70d5fff38f90b457a7c945ab59fc67c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52330869"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co jsou témata a předplatné služby Service Bus?
Témata a předplatné služby Service Bus podporují komunikační model zasílání zpráv *publikování/přihlášení*. Součásti distribuované aplikace při používání témat a předplatných nekomunikují navzájem přímo. Místo toho si zprávy vyměňují prostřednictvím tématu, které slouží jako zprostředkovatel.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Rozdíl od front služby Service Bus, ve kterém je každá zpráva zpracuje jeden spotřebitel, témata a předplatné nabízejí "jedna k mnoha" forma komunikace pomocí vzorec publikovat/odebírat. K jednomu tématu můžete zaregistrovat několik předplatných. Při odeslání zprávy do tématu bude zpráva zpřístupněná všem předplatným, aby ji nezávisle zpracovala.

Předplatné tématu se podobá virtuální frontě, která obdrží kopii zpráv, které byly odeslány do tématu. Máte taky možnost zaregistrovat pravidla filtrování určitého tématu na základě za předplatné. Pravidla filtru umožňují filtrovat nebo omezit přijímaných zpráv pro téma podle předplatných tématu.

Témata a odběry Service Bus umožňují škálování a zpracování velkého počtu zpráv mnoha uživatelů a aplikací.

## <a name="create-a-namespace"></a>Vytvoření oboru názvů
Pokud chcete začít používat témata a předplatné služby Service Bus v Azure, musíte nejdřív vytvořit *obor názvů služby*. Obor názvů poskytuje kontejner oboru pro adresování prostředků služby Service Bus v rámci vaší aplikace.

Vytvoření oboru názvů:

1. Přihlaste se na web [Azure Portal][Azure portal].
2. V levém navigačním podokně portálu klikněte na tlačítko **vytvořit prostředek**, pak klikněte na tlačítko **podniková integrace**a potom klikněte na tlačítko **služby Service Bus**.
3. V dialogovém okně **Vytvořit obor názvů** zadejte název oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.
4. Po kontrole, že je název oborů názvů k dispozici, zvolte cenovou úroveň (Basic, Standard nebo Premium).
5. V poli **Předplatné** zvolte předplatné Azure, ve které chcete vytvořit obor názvů.
6. V **skupiny prostředků** pole, vyberte existující skupinu prostředků, ve kterém se nachází oboru názvů nebo vytvořte novou.      
7. V poli **Umístění**, vyberte zemi nebo oblast, ve které by měl být oboru názvů hostován.
   
    ![Vytvoření oboru názvů][create-namespace]
8. Klikněte na tlačítko **Vytvořit**. Systém teď vytvoří obor názvů a povolí ho. Pravděpodobně budete muset několik minut počkat, než systém zřídí prostředky pro váš účet.

### <a name="obtain-the-credentials"></a>Získání přihlašovacích údajů
1. V seznamu oborů názvů klikněte na nově vytvořený obor názvů.
2. V **oboru názvů Service Bus** podokně klikněte na tlačítko **zásady sdíleného přístupu**.
3. V **zásady sdíleného přístupu** podokně klikněte na tlačítko **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. V **zásada: RootManageSharedAccessKey** podokně, klikněte na tlačítko kopírování vedle **připojovací řetězec – primární klíč**, zkopírujte připojovací řetězec do schránky pro pozdější použití.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png



---
title: Integrace RabbitMQ s Azure Service Bus
description: Podrobný průvodce integrací RabbitMQ s Azure Service Bus
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 71ea4dfcc164d5b8a8ba8bb411d529ce58a68f4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933734"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>Integrace RabbitMQ s Azure Service Bus

V této příručce se dozvíte, jak odesílat zprávy z RabbitMQ do Azure Service Bus.

Tady je několik scénářů, ve kterých můžeme využít tyto možnosti:

- **Nastavení Edge**: máme nastavení Edge, kde posíláme zprávy do RabbitMQ, ale chceme tyto zprávy přeposílat do [Azure Service Bus](./service-bus-messaging-overview.md) pro další zpracování, takže můžeme použít spoustu [funkcí Azure pro velké](/azure/architecture/guide/architecture-styles/big-data)objemy dat.
- **Hybridní cloud**: vaše společnost právě získala třetí stranu, která pro potřeby zasílání zpráv používá RabbitMQ. Jsou na jiném cloudu. Při přechodu na Azure už můžete začít sdílet data přemostěním RabbitMQ s Azure Service Bus.
- **Integrace třetích stran**: třetí strana používá RabbitMQ jako zprostředkovatele a chce odesílat data do nás, ale jsou mimo naši organizaci. Můžeme jim poskytnout klíč SAS, který jim poskytne přístup k omezené sadě Azure Service Bus frontám, do kterých mohou předávat své zprávy.

V seznamu se nachází, ale většinu těchto případů můžete vyřešit přemostěním RabbitMQ do Azure.

Nejdřív je potřeba vytvořit si bezplatný účet Azure tak, že se [sem](https://azure.microsoft.com/free/) zaregistrujete.

Jakmile se přihlásíte ke svému účtu, přejdete na [Azure Portal](https://portal.azure.com/) a vytvoříte nový [obor názvů](./service-bus-create-namespace-portal.md)Azure Service Bus. Obory názvů jsou oborové kontejnery, ve kterých se naše součásti zasílání zpráv budou živě, jako jsou fronty a témata.

## <a name="adding-a-new-azure-service-bus-namespace"></a>Přidává se nový obor názvů Azure Service Bus.

V Azure Portal klikněte na velké tlačítko plus a přidejte nový prostředek.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="Vytvoření prostředku":::

Pak vyberte integrace a kliknutím na Azure Service Bus vytvořte obor názvů pro zasílání zpráv:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Výběr služby Azure Service Bus":::

Zobrazí se výzva k zadání informací o oboru názvů. Vyberte předplatné Azure, které chcete použít. Pokud [skupinu prostředků](../azure-resource-manager/management/manage-resource-groups-portal.md)nemáte, můžete vytvořit novou.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="Vytvoření oboru názvů":::

Použijte `rabbitmq` pro `Namespace name` , ale může to být cokoli, co potřebujete. Potom nastavte `East US` umístění. `Basic`Jako cenovou úroveň vyberte.

Pokud se všechno objevilo dobře, měla by se zobrazit následující obrazovka s potvrzením:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="Potvrzení vytvoření oboru názvů":::

Pak se vraťte na Azure Portal uvidíte svůj nový `rabbitmq` obor názvů. Kliknutím na něj získáte přístup k prostředku, abyste k němu mohli přidat frontu.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="Seznam prostředků s novým oborem názvů":::

## <a name="creating-our-azure-service-bus-queue"></a>Vytváření Azure Service Bus fronty

Teď, když máte obor názvů Azure Service Bus, klikněte na `Queues` tlačítko vlevo v části `Entities` , abyste mohli přidat novou frontu:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="Vytvořit frontu":::

Název fronty bude `from-rabbitmq` pouze připomenutí, kde jsou zprávy přicházející z. Všechny ostatní možnosti můžete ponechat jako výchozí, ale můžete je změnit, aby vyhovovaly potřebám vaší aplikace.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>Povolení modulu plug-in RabbitMQ Shovel

K odeslání zpráv z RabbitMQ do Azure Service Bus budeme používat [modul plug-in Shovel](https://www.rabbitmq.com/shovel.html) , který se dodává s RabbitMQ. Pomocí tohoto příkazu můžete povolit modul plug-in a jeho vizuální rozhraní:

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Možná budete muset spustit tento příkaz jako kořenový.

Teď je čas získat přihlašovací údaje potřebné pro připojení RabbitMQ k Azure.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>Připojení RabbitMQ k Azure Service Bus

Pro vaši frontu budete muset vytvořit [zásadu sdíleného přístupu](../storage/common/storage-sas-overview.md) (SAS), takže RabbitMQ může do ní publikovat zprávy. Zásady SAS umožňují určit, co externí strana může s vaším prostředkem dělat. Účelem je, aby RabbitMQ mohl odesílat zprávy, ale neposlouchal ani nespravuje fronty.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="Přidat zásady SAS":::

Zaškrtnete `Send` políčko a kliknutím na něj `Create` budete mít naše zásady SAS na místě.

Po vytvoření zásady klikněte na ni a zobrazte **primární připojovací řetězec**. Budeme ho používat k tomu, aby RabbitMQ komunikovat s Azure Service Bus:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="Získat zásady SAS":::

Než budete moct tento připojovací řetězec použít, budete ho muset převést na AMQP formát připojení RabbitMQ. Přejděte do [nástroje Převaděč připojovacího řetězce](https://red-mushroom-0f7446a0f.azurestaticapps.net/) a vložte svůj připojovací řetězec do formuláře, klikněte na tlačítko převést. Získáte připojovací řetězec, který je RabbitMQ připravený. (Tento web v prohlížeči spustí vše, aby se vaše data neodesílala přes kabel). Ke svému zdrojovému kódu můžete přistupovat na [GitHubu](https://github.com/videlalvaro/connstring_to_amqp).

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="Převést připojovací řetězec":::

Teď otevřete modul plug-in pro správu RabbitMQ v našich prohlížečích `http://localhost:15672/#/dynamic-shovels` a `Admin -> Shovel Management` pak na, kde můžete přidat nové Shovel, které postará o posílání zpráv z fronty RabbitMQ do fronty Azure Service Bus.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="Přidat Shovel RabbitMQ":::

Zde zavolejte své Shovel `azure` a vyberte `AMQP 0.9.1` jako zdrojový protokol. Na snímku obrazovky máme `amqp://` výchozí identifikátor URI, který vás připojí k místnímu serveru RabbitMQ. Nezapomeňte ho přizpůsobit vašemu aktuálnímu nasazení.

Na straně fronty můžete použít `azure` jako název vaší fronty. Pokud tato fronta neexistuje, vytvoří ji RabbitMQ za vás. Můžete také zvolit název fronty, která již existuje. Ostatní možnosti můžete ponechat jako výchozí.

Pak na `destination` straně věci zvolte `AMQP 1.0` jako protokol. Do tohoto `URI` pole zadejte připojovací řetězec, který jste získali z předchozího kroku, převedli jste převod řetězce připojení Azure na formát RabbitMQ. Měl by vypadat takto:

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

Do `Address` pole zadáte název vaší **fronty Azure Service Bus**, v tomto případě byla volána `from-rabbitmq` . Klikněte na `Add Shovel` a vaše instalace by měla být připravená na zahájení přijímání zpráv.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>Publikování zpráv z RabbitMQ do Azure Service Bus

V rozhraní pro správu RabbitMQ můžete přejít na `Queues` , vybrat `azure` frontu a vyhledat `Publish message` panel. Zobrazí se formulář, který vám umožní publikovat zprávy přímo do fronty. Pro náš příklad jsme připravujeme `fist message` jako `Payload` volání a `Publish Message` :

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="Publikovat první zprávu":::

Vraťte se do Azure a prověřte svou frontu. Klikněte na `Service Bus Explorer` levý panel a potom klikněte na tlačítko _Náhled_ . Pokud všechno proběhlo správně, zobrazí se ve vaší frontě jedna zpráva. Yay!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Fronta služby Azure Service Bus":::

Pojďme se ale ujistit, že se jedná o zprávu, kterou jste odeslali z RabbitMQ. Vyberte `Peek` kartu a kliknutím na `Peek` tlačítko načtěte poslední zprávy ve frontě. Kliknutím na zprávu zkontrolujete její obsah. Měli byste vidět něco jako na obrázku níže, kde `first message` je uvedený obrázek.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="Náhled fronty":::

## <a name="lets-recap"></a>Pojďme se rekapitulace

Blahopřejeme! Dosáhli jste hodně! Spravovali jste, abyste získali zprávy ze RabbitMQ do Azure Service Bus, Rekapitulace kroky:

1. Vytvoření oboru názvů Azure Service Bus
2. Přidání fronty do oboru názvů
3. Přidání zásad SAS do fronty
4. Získání připojovacího řetězce fronty
5. Povolení modulu plug-in RabbitMQ Shovel & rozhraní pro správu
6. Převod připojovacího řetězce Azure Service Bus na formát AMQP RabbitMQ
7. Přidat nový Shovel do RabbitMQ & připojit k Azure Service Bus
8. Publikování zpráv

Podle předchozích kroků můžete integrovat oblasti vaší organizace mimo Azure. Modul plug-in Shovel vám umožní dodávat zprávy z RabbitMQ do Azure Service Bus. To má obrovské výhody, protože teď můžete důvěryhodným třetím stranám dovolit připojit své aplikace k nasazení Azure.

V tomto případě zasílání zpráv o povolení připojení a tato technika právě otevřela nové.

## <a name="next-steps"></a>Další kroky

- Další informace o [Azure Service Bus](./service-bus-messaging-overview.md)
- Další informace o [podpoře AMQP 1,0 v Service Bus](./service-bus-amqp-overview.md)

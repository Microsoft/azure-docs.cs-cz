---
title: Použití šablony ARM k publikování IoT Hub Azure, účtu úložiště, směrování zpráv
description: Použití šablony ARM k publikování IoT Hub Azure, účtu úložiště, směrování zpráv
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: 1b9c576ce03d808fe6a4d0cac5196dfcd1b73eab
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545476"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Rychlý Start: nasazení IoT Hub Azure a účtu úložiště pomocí šablony ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření IoT Hub, která bude směrovat zprávy do Azure Storage, a účet úložiště pro uložení zpráv. Po ručním přidání virtuálního zařízení IoT do centra pro odeslání zprávy nakonfigurujete informace o připojení v aplikaci s názvem  *ARM – čtení i zápis* pro odesílání zpráv ze zařízení do centra. Centrum je nakonfigurované tak, aby se zprávy odesílané do centra automaticky směrovaly do účtu úložiště. Na konci tohoto rychlého startu můžete otevřít účet úložiště a zobrazit odeslané zprávy.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure** . Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu se volá `101-iothub-auto-route-messages` ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages).

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

V šabloně jsou definované dva prostředky Azure:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>Nasaďte šablonu a spusťte ukázkovou aplikaci.

V této části najdete postup nasazení šablony, vytvoření virtuálního zařízení a spuštění aplikace ARM-Read-Write pro posílání zpráv.

1. Vytvořte prostředky nasazením šablony ARM.

    > [!TIP]
    > Kliknutím na tlačítko níže spustíte nasazení šablony. I když je spuštěný, nastavte aplikaci ARM-pro čtení a zápis, která se má spustit.

    [![Nasazení do Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. Stažení a extrahování [ukázek pro IoT C#](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/)

1. Otevřete okno příkazového řádku a přejdete do složky, ve které zrušíte ukázky pro IoT C#. Vyhledejte složku se souborem ARM-Read-Write. csproj. V tomto příkazovém okně vytvoříte proměnné prostředí. Pokud chcete získat klíče, přihlaste se k [Azure Portal](https://portal.azure.com) . Vyberte **skupiny prostředků** a pak vyberte skupinu prostředků použitou pro tento rychlý Start.

   ![Vyberte skupinu prostředků.](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. Zobrazí se IoT Hub a účet úložiště, který byl vytvořen při nasazení šablony ARM. Než budete pokračovat, počkejte, než se šablona úplně nasadí. Pak vyberte skupinu prostředků, abyste viděli své prostředky.

   ![Zobrazení prostředků ve skupině prostředků](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. Potřebujete **název centra** . V seznamu prostředků vyberte centrum. Zkopírujte název centra z horní části IoT Hub do schránky systému Windows.

   ![Zkopírovat název centra](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    V tomto příkazu nahraďte název centra a spusťte tento příkaz v příkazovém okně:

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   který bude vypadat jako v tomto příkladu:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. Další proměnnou prostředí je klíč zařízení IoT. Přidejte do centra nové zařízení tak, že v nabídce IoT Hub pro centrum vyberete **zařízení IoT** .

   ![Vybrat zařízení IoT](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. Na pravé straně obrazovky vyberte **+ Nová** a přidejte tak nové zařízení.

   Zadejte nový název zařízení. V tomto rychlém startu se používá název začínající **contoso-test-Device** . Uložte zařízení a pak znovu otevřete tuto obrazovku, abyste získali klíč zařízení. (Klíč se vygeneruje při zavření podokna.) Vyberte buď primární, nebo sekundární klíč, a zkopírujte ho do schránky Windows. V příkazovém okně nastavte příkaz, který má být spuštěn, a potom stiskněte klávesu **ENTER** . Příkaz by měl vypadat nějak takto, ale s klíčem zařízení vloženým v:

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. Poslední proměnnou prostředí je **ID zařízení** . V příkazovém okně nastavte příkaz a proveďte ho.

   ```cms
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   což bude vypadat jako v tomto příkladu:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Chcete-li zobrazit proměnné prostředí, které jste definovali, zadejte do příkazového řádku příkaz SET a stiskněte klávesu **ENTER** a vyhledejte ty, které začínají řetězcem **IoT** .

   ![Zobrazit proměnné prostředí](./media/horizontal-arm-route-messages/06-environment-variables.png)

    Nyní jsou proměnné prostředí nastaveny, spusťte aplikaci ze stejného příkazového okna. Vzhledem k tomu, že používáte stejné okno, proměnné budou přístupné v paměti při spuštění aplikace.

1. Chcete-li spustit aplikaci, zadejte do příkazového řádku následující příkaz a stiskněte klávesu **ENTER** .

    `dotnet run arm-read-write`

   Aplikace vygeneruje a zobrazí zprávy v konzole nástroje, když pošle každou zprávu do centra IoT. Centrum se nakonfigurovalo v šabloně ARM, aby mělo automatizované směrování. Zprávy obsahující text `level = storage` jsou automaticky směrovány do účtu úložiště. Nechte aplikaci spuštěnou 10 až 15 minut a pak stiskněte klávesu **ENTER** jednou nebo dvakrát, dokud přestane běžet.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a vyberte skupinu prostředků a pak vyberte účet úložiště.

1. Přejděte k podrobnostem o účtu úložiště, dokud nenajdete soubory.

   ![Podívejte se na soubory účtu úložiště.](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Vyberte jeden ze souborů a vyberte **Stáhnout** a stáhnout soubor do umístění, které můžete najít později. Bude mít název, který bude číselný, například 47. Přidejte _. txt_ na konec a potom poklikejte na soubor pro otevření.

1. Když soubor otevřete, bude každý řádek pro jinou zprávu. obsah každé zprávy je také zašifrovaný. Je nutné, abyste mohli provádět dotazy proti textu zprávy.

   ![Zobrazit odeslané zprávy](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Tyto zprávy jsou kódované v kódování UTF-32 a base64. Pokud si zprávu přečtete zpátky, musíte ji dekódovat z Base64 a UTF-32, aby ji bylo možné číst jako ASCII. Pokud vás zajímáte, můžete použít metodu ReadOneRowFromFile v kurzu směrování ke čtení jednoho z těchto souborů zpráv a jejich dekódování na ASCII. ReadOneRowFromFile je v úložišti ukázek pro IoT C#, které pro tento rychlý Start rozcházíte. Tady je cesta od horní části této složky: *./IoT-Hub/tutorials/Routing/SimulatedDevice/program.cs.* Nastavte logickou hodnotu `readTheFile` na true a nekódujte pevně cestu k souboru na disku a pak otevřete a Přeložte první řádek v souboru.

Nasadili jste šablonu ARM, která vytvoří IoT Hub a účet úložiště, a spustí program, který odešle zprávy do centra. Zprávy se pak automaticky uloží do účtu úložiště, kde je můžete zobrazit.

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání prostředků přidaných během tohoto rychlého startu se přihlaste k [Azure Portal](https://portal.azure.com). Vyberte **skupiny prostředků** a pak vyhledejte skupinu prostředků, kterou jste použili pro tento rychlý Start. Vyberte skupinu prostředků a pak vyberte *Odstranit* . Odstraní všechny prostředky ve skupině.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

---
title: Rychlý Start pro použití konfigurace aplikací Azure s aplikacemi Pythonu | Microsoft Docs
description: V tomto rychlém startu vytvoříte aplikaci v Pythonu s konfigurací aplikace Azure, která bude centralizovat úložiště a správu nastavení aplikace odděleně od vašeho kódu.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 954f4edcd10d701d00d9cd23280aaac7c287992d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91997465"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace v Pythonu pomocí konfigurace aplikace Azure

V tomto rychlém startu použijete Azure App Configuration k centralizaci úložiště a správy nastavení aplikace pomocí [klientské knihovny pro konfiguraci aplikací Azure pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- Python 2,7 nebo 3,5 nebo novější – informace o nastavení Pythonu ve Windows najdete v [dokumentaci k Pythonu v systému Windows]( https://docs.microsoft.com/windows/python/) .

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Vyberte možnost **Průzkumník konfigurace**  >  **vytvořit**  >  **klíč-hodnota** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

8. Vyberte **Použít**.

## <a name="setting-up-the-python-app"></a>Nastavení aplikace v Pythonu

1. V tomto kurzu vytvoříte nový adresář pro projekt s názvem *App-Configuration-rychlý Start*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Přepněte na nově vytvořený adresář *App-Configuration-Starter* .

    ```console
    cd app-configuration-quickstart
    ```

1. Pomocí příkazu nainstalujte klientskou knihovnu konfigurace Azure App Configuration `pip install` .

    ```console
    pip install azure-appconfiguration
    ```

1. V adresáři *App-Configuration-Starter* vytvořte nový soubor s názvem *App-Configuration-quickstart.py* a přidejte následující kód:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Fragmenty kódu v tomto rychlém startu vám pomůžou začít s klientskou knihovnou konfigurace aplikace pro Python. Pro vaši aplikaci byste měli také zvážit zpracování výjimek podle vašich potřeb. Další informace o zpracování výjimek najdete v naší [dokumentaci k sadě Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="configure-your-app-configuration-connection-string"></a>Konfigurace připojovacího řetězce konfigurace aplikace

1. Nastavte proměnnou prostředí s názvem **AZURE_APP_CONFIG_CONNECTION_STRING** a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Na příkazovém řádku spusťte následující příkaz:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Restartováním příkazového řádku umožníte, aby se změna projevila. Vytiskněte hodnotu proměnné prostředí, abyste ověřili, jestli je správně nastavená.

## <a name="code-samples"></a>Ukázky kódů

Ukázkové fragmenty kódu v této části ukazují, jak provádět běžné operace s klientskou knihovnou konfigurace aplikací pro Python. Přidejte tyto fragmenty kódu do `try` bloku v souboru *App-Configuration-quickstart.py* , který jste vytvořili dříve.

> [!NOTE]
> Klientská knihovna konfigurace aplikace odkazuje na objekt hodnoty klíč-hodnota `ConfigurationSetting` . Proto se v tomto článku v úložišti konfigurace aplikace budou označovat **hodnoty klíč-hodnota** **nastavení konfigurace**.

* [Připojení k úložišti konfigurace aplikace](#connect-to-an-app-configuration-store)
* [Získat nastavení konfigurace](#get-a-configuration-setting)
* [Přidat nastavení konfigurace](#add-a-configuration-setting)
* [Získat seznam nastavení konfigurace](#get-a-list-of-configuration-settings)
* [Uzamknout nastavení konfigurace](#lock-a-configuration-setting)
* [Odemknout nastavení konfigurace](#unlock-a-configuration-setting)
* [Aktualizace nastavení konfigurace](#update-a-configuration-setting)
* [Odstraní nastavení konfigurace.](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

Následující fragment kódu vytvoří instanci třídy **AzureAppConfigurationClient** pomocí připojovacího řetězce uloženého ve vašich proměnných prostředí.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Získat nastavení konfigurace

Následující fragment kódu načte nastavení konfigurace podle `key` názvu.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Přidat nastavení konfigurace

Následující fragment kódu vytvoří `ConfigurationSetting` objekt s `key` `value` poli a a vyvolá `add_configuration_setting` metodu. Tato metoda vyvolá výjimku, pokud se pokusíte přidat nastavení konfigurace, které již existuje ve vašem úložišti. Chcete-li se této výjimce vyhnout, lze místo toho použít metodu [set_configuration_setting](#update-a-configuration-setting) .

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Získat seznam nastavení konfigurace

Následující fragment kódu načte seznam nastavení konfigurace. `key_filter`Argumenty a `label_filter` lze zadat pro filtrování hodnot klíčů na základě `key` a v `label` uvedeném pořadí. Další informace o filtrování najdete v tématu Postup při [dotazování na nastavení konfigurace](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Uzamknout nastavení konfigurace

Stav zámku hodnoty klíč-hodnota v konfiguraci aplikace je označen `read_only` atributem `ConfigurationSetting` objektu. Pokud `read_only` je `True` , nastavení je uzamčené. `set_read_only`Metodu lze vyvolat pomocí `read_only=True` argumentu pro uzamknutí nastavení konfigurace.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Odemknout nastavení konfigurace

Pokud má `read_only` atribut hodnotu `ConfigurationSetting` `False` , nastavení se odemkne. `set_read_only`Metodu lze vyvolat s `read_only=False` argumentem pro odemčení nastavení konfigurace.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Aktualizace nastavení konfigurace

`set_configuration_setting`Metodu lze použít k aktualizaci stávajícího nastavení nebo vytvoření nového nastavení. Následující fragment kódu změní hodnotu stávajícího nastavení konfigurace.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Odstraní nastavení konfigurace.

Následující fragment kódu odstraní konfigurační nastavení podle `key` názvu.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Spuštění aplikace

V tomto rychlém startu jste vytvořili aplikaci v Pythonu, která pomocí klientské knihovny konfigurace Azure App Configuration načetla nastavení konfigurace vytvořené prostřednictvím Azure Portal, přidá nové nastavení, načte seznam existujících nastavení, zamkne a odemkne nastavení, aktualizuje nastavení a nakonec odstraní nastavení.

V tomto okamžiku by měl mít soubor *App-Configuration-quickstart.py* následující kód:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

V okně konzoly přejděte do adresáře, který obsahuje soubor *App-Configuration-quickstart.py* , a spusťte následující příkaz Pythonu pro spuštění aplikace:

```console
python app-configuration-quickstart.py
```

Měl by se zobrazit následující výstup:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Vyčištění prostředků


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikací a naučíte se, jak získat přístup k klíčovým hodnotám z aplikace v Pythonu.

Další ukázky kódu najdete na adrese:

> [!div class="nextstepaction"]
> [Ukázky klientské knihovny pro konfiguraci aplikací Azure](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
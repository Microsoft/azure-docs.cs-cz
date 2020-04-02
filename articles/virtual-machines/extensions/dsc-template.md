---
title: Rozšíření konfigurace požadovaného stavu se šablonami Azure Resource Manageru
description: Přečtěte si o definici šablony Správce prostředků pro rozšíření Konfigurace požadovaného stavu (DSC) v Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: Dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: 89bae60c30cfe82d38c61f385dbaef574b4152d8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547783"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Rozšíření konfigurace požadovaného stavu se šablonami Azure Resource Manageru

Tento článek popisuje šablonu Azure Resource Manager pro [obslužnou rutinu rozšíření Konfigurace požadovaného stavu (DSC).](dsc-overview.md) Mnoho příkladů používá **registrationurl** (poskytované jako řetězec) a **RegistrationKey** (poskytované jako [PSCredential)](/dotnet/api/system.management.automation.pscredential)k nazabezpečení s Azure Automation. Podrobnosti o získání těchto hodnot naleznete [v tématu Onboarding machines for management by Azure Automation State Configuration – Secure registration](/azure/automation/automation-dsc-onboarding#onboarding-securely-using-registration).

> [!NOTE]
> Můžete se setkat s mírně odlišnými příklady schématu. Ke změně schématu došlo ve verzi z října 2016. Podrobnosti naleznete v [tématu Aktualizace z předchozího formátu](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Příklad šablony pro virtuální počítače se systémem Windows

Následující úryvek přejde do části **Zdroj** šablony.
Rozšíření DSC dědí výchozí vlastnosti rozšíření.
Další informace naleznete v tématu [VirtualMachineExtension class](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-06-30",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Příklad šablony pro škálovací sady virtuálních strojů windows

Uzel škálovací sady virtuálního počítače má oddíl **vlastností,** který má atribut **VirtualMachineProfile, extensionProfile.**
V **části rozšíření**přidejte podrobnosti pro rozšíření DSC.

Rozšíření DSC dědí výchozí vlastnosti rozšíření.
Další informace naleznete v tématu [VirtualMachineScaleSetExtension class](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Podrobné informace o nastavení

Použijte následující schéma v části **nastavení** rozšíření Azure DSC v šabloně Správce prostředků.

Seznam argumentů, které jsou k dispozici pro výchozí konfigurační skript, naleznete [v tématu Výchozí konfigurační skript](#default-configuration-script).

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>Podrobnosti

| Název vlastnosti | Typ | Popis |
| --- | --- | --- |
| verze settings.wmfVersion |řetězec |Určuje verzi rozhraní WMF (Windows Management Framework), která by měla být nainstalována na vašem virtuálním počítači. Nastavení této vlastnosti na **nejnovější** nainstaluje nejnovější verzi WMF. V současné době jsou pouze možné hodnoty pro tuto vlastnost **4.0**, **5.0**, **5.1**a **nejnovější**. Tyto možné hodnoty podléhají aktualizacím. Výchozí hodnota je **nejnovější**. |
| adresu settings.configuration.url |řetězec |Určuje umístění adresy URL, ze kterého chcete stáhnout soubor ZIP konfigurace DSC. Pokud zadaná adresa URL vyžaduje pro přístup token SAS, nastavte vlastnost **protectedSettings.configurationUrlSasToken** na hodnotu tokenu SAS. Tato vlastnost je vyžadována, pokud jsou definovány **funkce settings.configuration.script** nebo **settings.configuration.function.** Pokud pro tyto vlastnosti není zadána žádná hodnota, rozšíření zavolá výchozí konfigurační skript pro nastavení metadat nástroje Location Configuration Manager (LCM) a měly by být zadány argumenty. |
| settings.configuration.script |řetězec |Určuje název souboru skriptu, který obsahuje definici konfigurace DSC. Tento skript musí být v kořenové složce souboru ZIP, který je stažen z adresy URL určené vlastností **settings.configuration.url.** Tato vlastnost je vyžadována, pokud jsou definovány **soubory settings.configuration.url** nebo **settings.configuration.script.** Pokud pro tyto vlastnosti není zadána žádná hodnota, rozšíření zavolá výchozí konfigurační skript pro nastavení metadat LCM a měly by být zadány argumenty. |
| settings.configuration.function |řetězec |Určuje název konfigurace DSC. Konfigurace s názvem musí být zahrnuta do skriptu, který definuje **soubor settings.configuration.script.** Tato vlastnost je vyžadována, pokud jsou definovány **soubory settings.configuration.url** nebo **settings.configuration.function.** Pokud pro tyto vlastnosti není zadána žádná hodnota, rozšíření zavolá výchozí konfigurační skript pro nastavení metadat LCM a měly by být zadány argumenty. |
| settings.configurationArguments |Kolekce |Definuje všechny parametry, které chcete předat konfiguraci DSC. Tato vlastnost není šifrována. |
| settings.configurationData.url |řetězec |Určuje adresu URL, ze které chcete stáhnout konfigurační data (.psd1), která má být používána jako vstup pro konfiguraci DSC. Pokud zadaná adresa URL vyžaduje pro přístup token SAS, nastavte vlastnost **protectedSettings.configurationDataUrlSasToken** na hodnotu tokenu SAS. |
| settings.privacy.dataCollection |řetězec |Povolí nebo zakáže kolekci telemetrie. Jediné možné hodnoty pro tuto vlastnost jsou **Povolit**, **Zakázat**, **''** nebo **$null**. Ponecháte-li tuto vlastnost prázdnou nebo nulovou, povoluje telemetrická data. Výchozí hodnota je **''**. Další informace naleznete v [tématu Azure DSC rozšíření kolekce dat](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Kolekce |Definuje alternativní umístění, ze kterých chcete stáhnout WMF. Další informace naleznete v [tématu Rozšíření Azure DSC 2.8 a jak mapovat stahování závislostí rozšíření do vlastního umístění](https://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Kolekce |Definuje všechny parametry, které chcete předat konfiguraci DSC. Tato vlastnost je zašifrována. |
| protectedSettings.configurationUrlSasToken |řetězec |Určuje token SAS, který se má použít pro přístup k adrese URL, kterou definuje **soubor settings.configuration.url.** Tato vlastnost je zašifrována. |
| protectedSettings.configurationDataUrlSasToken |řetězec |Určuje token SAS, který se má použít pro přístup k adrese URL, kterou definuje **soubor settings.configurationData.url.** Tato vlastnost je zašifrována. |

## <a name="default-configuration-script"></a>Výchozí konfigurační skript

Další informace o následujících hodnotách naleznete v [tématu Local Configuration Manager Basic settings](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Výchozí konfigurační skript rozšíření DSC můžete použít ke konfiguraci pouze vlastností LCM, které jsou uvedeny v následující tabulce.

| Název vlastnosti | Typ | Popis |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |Pověření PS |Požadovaná vlastnost. Určuje klíč, který se používá pro uzel k registraci se službou Azure Automation jako heslo objektu pověření Prostředí PowerShell. Tuto hodnotu lze automaticky zjistit pomocí metody **listkeys** proti účtu Automation.  Viz [příklad](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments.RegistrationUrl |řetězec |Požadovaná vlastnost. Určuje adresu URL koncového bodu automatizace, kde se uzel pokusí zaregistrovat. Tuto hodnotu lze automaticky zjistit pomocí **referenční** metody proti účtu Automatizace. |
| settings.configurationArguments.NodeConfigurationName |řetězec |Požadovaná vlastnost. Určuje konfiguraci uzlu v účtu Automatizace, který má být přiřazen uzlu. |
| settings.configurationArguments.ConfigurationMode |řetězec |Určuje režim lcm. Mezi platné možnosti patří **ApplyOnly**, **ApplyandMonitor**a **ApplyandAutoCorrect**.  Výchozí hodnota je **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | Uint32 | Určuje, jak často se LCM pokouší zkontrolovat s účtem automatizace aktualizace.  Výchozí hodnota je **30**.  Minimální hodnota je **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | Uint32 | Určuje, jak často lcm ověřuje aktuální konfiguraci. Výchozí hodnota je **15**. Minimální hodnota je **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | Boolean | Určuje, zda lze uzel automaticky restartovat, pokud o něj operace DSC požádá. Výchozí hodnota je **false**. |
| settings.configurationArguments.ActionAfterReboot | řetězec | Určuje, co se stane po restartování při použití konfigurace. Platné možnosti jsou **ContinueConfiguration** a **StopConfiguration**. Výchozí hodnota je **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | Boolean | Určuje, zda lcm přepíše existující moduly v uzlu. Výchozí hodnota je **false**. |

## <a name="settings-vs-protectedsettings"></a>nastavení vs. protectedSettings

Všechna nastavení se uloží do textového souboru nastavení na virtuálním počítači.
Vlastnosti uvedené v části **nastavení** jsou veřejné vlastnosti.
Veřejné vlastnosti nejsou v textovém souboru nastavení šifrovány.
Vlastnosti uvedené v části **protectedSettings** jsou šifrovány pomocí certifikátu a nejsou zobrazeny ve formátu prostého textu v souboru nastavení na virtuálním počítači.

Pokud konfigurace potřebuje pověření, můžete je zahrnout do **protectedSettings**:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Ukázkový konfigurační skript

Následující příklad ukazuje výchozí chování pro rozšíření DSC, což je poskytnout nastavení metadat LCM a zaregistrovat se službou Automatizace DSC.
Jsou vyžadovány argumenty konfigurace.
Konfigurační argumenty jsou předány do výchozího konfiguračního skriptu pro nastavení metadat LCM.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Příklad použití konfiguračního skriptu ve službě Azure Storage

Následující příklad je z [přehledu obslužné rutiny rozšíření DSC](dsc-overview.md).
Tento příklad používá šablony Správce prostředků namísto rutin k nasazení rozšíření.
Uložte konfiguraci IisInstall.ps1, umístěte ji do `iisinstall.zip`souboru ZIP (například: ) a nahrajte soubor do přístupné adresy URL.
Tento příklad používá úložiště objektů Blob Azure, ale můžete stáhnout soubory ZIP z libovolného umístění.

V šabloně Správce prostředků následující kód instruuje virtuálního počítače ke stažení správného souboru a pak spustit příslušnou funkci Prostředí PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Příklad použití odkazovaných registračních hodnot Azure Automation

Následující příklad získá **RegistrationUrl** a **RegistrationKey** odkazem na vlastnosti účtu Azure Automation a pomocí **listkeys** metoda načíst primární klíč (0).  V tomto příkladu byly šabloně poskytnuty parametry **automationAccountName** a **NodeConfigName.**

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Aktualizace z předchozího formátu

Všechna nastavení v předchozím formátu rozšíření (které mají veřejné vlastnosti **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken**nebo **Vlastnosti**) automaticky přizpůsobí aktuálnímu formátu rozšíření.
Utíkají stejně jako předtím.

Následující schéma ukazuje, jak vypadalo předchozí schéma nastavení:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Předchozí formát se přizpůsobí aktuálnímu formátu takto:

| Aktuální název vlastnosti | Předchozí ekvivalent schématu |
| --- | --- |
| verze settings.wmfVersion |Nastavení. WMFVersion |
| adresu settings.configuration.url |Nastavení. Adresa URL modulů |
| settings.configuration.script |První část nastavení. ConfigurationFunction (před \\ \\) |
| settings.configuration.function |Druhá část nastavení. ConfigurationFunction (po \\ \\) |
| settings.configuration.module.name | Nastavení. Zdroj modulu |
| settings.configuration.module.version | Nastavení. ModulVerze |
| settings.configurationArguments |Nastavení. Vlastnosti |
| settings.configurationData.url |protectedSettings.DataBlobUri (bez tokenu SAS) |
| settings.privacy.dataCollection |Nastavení. Privacy.dataCollection |
| settings.advancedOptions.downloadMappings |Nastavení. RozšířenéMožnosti.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |Nastavení. Token Sas |
| protectedSettings.configurationDataUrlSasToken |Token SAS z protectedSettings.DataBlobUri |

## <a name="troubleshooting"></a>Řešení potíží

Zde jsou některé z chyb, které můžete narazit, a jak je můžete opravit.

### <a name="invalid-values"></a>Neplatné hodnoty

"Privacy.dataCollection je{0}' '.
Jediné možné hodnoty jsou '', 'Povolit', a 'Zakázat'".
"WmfVersion je{0}' .
Pouze možné hodnoty jsou ... a "nejnovější".

**Problém**: Zadaný hodnota není povolena.

**Řešení:** Změňte neplatnou hodnotu na platnou hodnotu.
Další informace naleznete v tabulce [podrobnosti](#details).

### <a name="invalid-url"></a>Neplatná adresa URL

"ConfigurationData.url je{0}' '. Toto není platná adresa URL" "DataBlobUri je '{0}'. Toto není platná adresa URL" Configuration.url is '{0}.. Toto není platná adresa URL"

**Problém**: Zadaná adresa URL není platná.

**Řešení:** Zkontrolujte všechny poskytnuté adresy URL.
Ujistěte se, že všechny adresy URL se přemístí do platných umístění, ke kterým má rozšíření přístup ve vzdáleném počítači.

### <a name="invalid-registrationkey-type"></a>Neplatný typ registračního klíče

"Neplatný typ parametru RegistrationKey typu PSCredential."

**Problém**: Hodnotu *RegistrationKey* v protectedSettings.configurationArguments nelze poskytnout jako jiný typ než PSCredential.

**Řešení**: Změňte položku protectedSettings.configurationArguments pro RegistrationKey na typ PSCredential pomocí následujícího formátu:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Neplatný typ Typu ConfigurationArgument

"Neplatný typ {0}argumentů konfigurace "

**Problém**: Vlastnost *ConfigurationArguments* nelze vyřešit objekt **uhas tabulky.**

**Řešení:** Vytvořte vlastnost *ConfigurationArguments* a **tabulku Hash**.
Postupujte podle formátu uvedeného v předchozích příkladech. Pozor na uvozovky, čárky a rovnátka.

### <a name="duplicate-configurationarguments"></a>Duplicitní argumenty konfigurace

"Nalezeno duplicitních{0}argumentů " ve veřejné i chráněné konfiguraci Arguments"

**Problém**: *ConfigurationArguments* ve veřejném nastavení a *ConfigurationArguments* v chráněném nastavení mají vlastnosti se stejným názvem.

**Řešení:** Odeberte jednu z duplicitních vlastností.

### <a name="missing-properties"></a>Chybějící vlastnosti

"nastavení. Configuration.function vyžaduje, aby bylo zadáno souboru settings.configuration.url nebo settings.configuration.module"

"nastavení. Adresa Configuration.url vyžaduje, aby byl zadán soubor settings.configuration.script"

"nastavení. Configuration.script vyžaduje, aby byl zadán soubor settings.configuration.url.

"nastavení. Adresa Configuration.url vyžaduje, aby byla zadána funkce settings.configuration.function"

"protectedSettings.ConfigurationUrlSasToken vyžaduje, aby bylo zadáno souboru settings.configuration.url"

"protectedSettings.ConfigurationDataUrlSasToken vyžaduje, aby bylo zadáno nastavení.configurationData.url"

**Problém**: Definovaná vlastnost potřebuje jinou vlastnost, která chybí.

**Řešení**:

- Poskytněte chybějící vlastnost.
- Odeberte vlastnost, která potřebuje chybějící vlastnost.

## <a name="next-steps"></a>Další kroky

- Další informace o [používání škálovacích sad virtuálních strojů s rozšířením Azure DSC](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Další podrobnosti o [zabezpečené správě přihlašovacích údajů dsc](dsc-credentials.md).
- Získejte [úvod do obslužné rutiny rozšíření Azure DSC](dsc-overview.md).
- Další informace o prostředí PowerShell DSC naleznete v [dokumentačním centru prostředí PowerShell](/powershell/scripting/dsc/overview/overview).

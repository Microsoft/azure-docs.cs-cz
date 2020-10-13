---
title: Požadovaná rozšíření konfigurace stavu pomocí šablon Azure Resource Manager
description: Přečtěte si o definici šablony Správce prostředků pro rozšíření konfigurace požadovaného stavu (DSC) v Azure.
services: virtual-machines-windows
author: bobbytreed
manager: carmonm
tags: azure-resource-manager
keywords: DSC
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/05/2018
ms.author: robreed
ms.openlocfilehash: dc73b5b9f05d24de206b25095ea7eaf93f035298
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511156"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Požadovaná rozšíření konfigurace stavu pomocí šablon Azure Resource Manager

Tento článek popisuje šablonu Azure Resource Manager pro [obslužnou rutinu rozšíření požadované konfigurace stavu (DSC)](dsc-overview.md). Mnohé z příkladů používají **RegistrationURL** (poskytované jako řetězec) a **RegistrationKey** (poskytované jako [PSCredential](/dotnet/api/system.management.automation.pscredential) pro začlenění Azure Automation. Podrobnosti o získání těchto hodnot najdete v tématu věnovaném [připojování počítačů pro správu pomocí konfigurace stavu Azure Automation – zabezpečená registrace](../../automation/automation-dsc-onboarding.md#enable-machines-securely-using-registration).

> [!NOTE]
> Můžete se setkat s trochu odlišnými příklady schématu. Změna schématu se stala ve verzi z října 2016. Podrobnosti najdete v tématu [aktualizace z předchozího formátu](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Příklad šablony pro virtuální počítač s Windows

Následující fragment kódu přejde do části **prostředků** v šabloně.
Rozšíření DSC dědí výchozí vlastnosti rozšíření.
Další informace naleznete v tématu [Třída VirtualMachineExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet).

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

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Příklad šablony pro Windows Virtual Machine Scale Sets

Uzel sady škálování virtuálního počítače má oddíl **vlastností** , který má atribut **VirtualMachineProfile, extensionProfile** .
V části **rozšíření**přidejte podrobnosti o rozšíření DSC.

Rozšíření DSC dědí výchozí vlastnosti rozšíření.
Další informace naleznete v tématu [Třída VirtualMachineScaleSetExtension](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

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

V části **Nastavení** rozšíření Azure DSC v šabloně správce prostředků použijte následující schéma.

Seznam argumentů, které jsou k dispozici pro výchozí konfigurační skript, najdete v tématu [výchozí konfigurační skript](#default-configuration-script).

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

| Název vlastnosti | Typ | Description |
| --- | --- | --- |
| Settings. wmfVersion |řetězec |Určuje verzi rozhraní Windows Management Framework (WMF), která má být nainstalována na VIRTUÁLNÍm počítači. Nastavení této vlastnosti na **nejnovější** nainstaluje nejnovější verzi WMF. V současné době jsou jedinou možnou hodnotou této vlastnosti **4,0**, **5,0**, **5,1**a **nejnovější**. Tyto možné hodnoty se vztahují na aktualizace. Výchozí hodnota je **nejnovější**. |
| settings.configuration. URL |řetězec |Určuje umístění adresy URL, ze kterého se má stáhnout soubor. zip konfigurace DSC. Pokud zadaná adresa URL vyžaduje pro přístup token SAS, nastavte vlastnost **protectedSettings.configurationUrlSasToken** na hodnotu vašeho tokenu SAS. Tato vlastnost je povinná, pokud je definována **settings.configuration. Script** nebo **settings.configuration. Function** . Pokud se pro tyto vlastnosti nezadá žádná hodnota, přípona volá výchozí konfigurační skript pro nastavení Configuration Manageru (LCM) metadata a měly by se zadat argumenty. |
| settings.configuration. Script |řetězec |Určuje název souboru skriptu, který obsahuje definici konfigurace DSC. Tento skript musí být v kořenové složce souboru. zip, který je stažený z adresy URL určené vlastností **settings.configuration. URL** . Tato vlastnost je povinná, pokud jsou definovány **settings.configuration. URL** nebo **settings.configuration. Script** . Pokud pro tyto vlastnosti není zadána žádná hodnota, přípona volá výchozí konfigurační skript pro nastavení metadat LCM a argumenty by měly být zadány. |
| settings.configuration. Function |řetězec |Určuje název konfigurace DSC. Nazvaná konfigurace musí být součástí skriptu, který **settings.configuration. Script** definuje. Tato vlastnost je povinná, pokud jsou definovány **settings.configuration. URL** nebo **settings.configuration. Function** . Pokud pro tyto vlastnosti není zadána žádná hodnota, přípona volá výchozí konfigurační skript pro nastavení metadat LCM a argumenty by měly být zadány. |
| settings.configurationArguments |Kolekce |Definuje všechny parametry, které chcete předat konfiguraci DSC. Tato vlastnost není šifrovaná. |
| settings.configurationData. URL |řetězec |Určuje adresu URL, ze které se má stáhnout soubor konfiguračních dat (. psd1), který se použije jako vstup pro konfiguraci DSC. Pokud zadaná adresa URL vyžaduje pro přístup token SAS, nastavte vlastnost **protectedSettings.configurationDataUrlSasToken** na hodnotu vašeho tokenu SAS. |
| Settings. Privacy. DataCollection |řetězec |Povolí nebo zakáže shromažďování telemetrie. Jedinou možnou hodnotou této vlastnosti je **Enable**, **Disable** **, nebo** **$null**. Tato vlastnost zůstane prázdná nebo má hodnotu null. umožňuje telemetrii. Výchozí hodnota je ' **'**. Další informace najdete v tématu [shromažďování dat rozšíření Azure DSC](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/). |
| Settings. advancedOptions. downloadMappings |Kolekce |Definuje alternativní umístění, ze kterých se má stáhnout WMF. Další informace najdete v tématu [rozšíření Azure DSC 2,8 a postup mapování stahování závislostí rozšíření na vaše vlastní umístění](https://devblogs.microsoft.com/powershell/azure-dsc-extension-2-8-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location/). |
| protectedSettings.configurationArguments |Kolekce |Definuje všechny parametry, které chcete předat konfiguraci DSC. Tato vlastnost je zašifrovaná. |
| protectedSettings.configurationUrlSasToken |řetězec |Určuje token SAS, který se má použít pro přístup k adrese URL, kterou **settings.configuration. URL** definuje. Tato vlastnost je zašifrovaná. |
| protectedSettings.configurationDataUrlSasToken |řetězec |Určuje token SAS, který se má použít pro přístup k adrese URL, kterou  **settings.configurationData. URL** definuje. Tato vlastnost je zašifrovaná. |

## <a name="default-configuration-script"></a>Výchozí konfigurační skript

Další informace o následujících hodnotách naleznete v tématu [místní nastavení Configuration Manager Basic](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Můžete použít výchozí konfigurační skript rozšíření DSC ke konfiguraci pouze vlastností LCM, které jsou uvedeny v následující tabulce.

| Název vlastnosti | Typ | Description |
| --- | --- | --- |
| protectedSettings.configurationArguments. RegistrationKey |PSCredential |Požadovaná vlastnost. Určuje klíč, který se používá pro uzel, který se má zaregistrovat ve službě Azure Automation jako heslo objektu přihlašovacích údajů prostředí PowerShell. Tato hodnota se dá automaticky zjistit pomocí metody **klíče listkey** na účtu Automation.  Podívejte se na [příklad](#example-using-referenced-azure-automation-registration-values). |
| settings.configurationArguments. RegistrationUrl |řetězec |Požadovaná vlastnost. Určuje adresu URL koncového bodu automatizace, kde se uzel pokusí zaregistrovat. Tato hodnota se může automaticky zjistit pomocí metody **reference** k účtu Automation. |
| settings.configurationArguments. NodeConfigurationName |řetězec |Požadovaná vlastnost. Určuje konfiguraci uzlu v účtu Automation, která se přiřadí k uzlu. |
| settings.configurationArguments.ConfigurationMode |řetězec |Určuje režim pro LCM. Mezi platné možnosti patří **ApplyOnly**, **ApplyandMonitor**a **ApplyandAutoCorrect**.  Výchozí hodnota je **ApplyandMonitor**. |
| settings.configurationArguments. RefreshFrequencyMins | UInt32 | Určuje, jak často se při pokusu o aktualizaci v účtu Automation pokusí zjistit aktualizace.  Výchozí hodnota je **30**.  Minimální hodnota je **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | UInt32 | Určuje, jak často funkce LCM ověřuje aktuální konfiguraci. Výchozí hodnota je **15**. Minimální hodnota je **15**. |
| settings.configurationArguments. RebootNodeIfNeeded | boolean | Určuje, zda může být uzel automaticky restartován, pokud na něj operace DSC žádá. Výchozí hodnota je **false**. |
| settings.configurationArguments. ActionAfterReboot | řetězec | Určuje, co se stane po restartování při použití konfigurace. Platné možnosti jsou **ContinueConfiguration** a **StopConfiguration**. Výchozí hodnota je **ContinueConfiguration**. |
| settings.configurationArguments. AllowModuleOverwrite | boolean | Určuje, zda LCM Přepisuje existující moduly na uzlu. Výchozí hodnota je **false**. |

## <a name="settings-vs-protectedsettings"></a>nastavení vs. protectedSettings

Všechna nastavení se ukládají do textového souboru nastavení na virtuálním počítači.
Vlastnosti uvedené v části **Nastavení** jsou veřejné vlastnosti.
Veřejné vlastnosti nejsou v textovém souboru nastavení šifrované.
Vlastnosti uvedené v části **protectedSettings** se šifrují pomocí certifikátu a v souboru nastavení na virtuálním počítači se nezobrazuje jako prostý text.

Pokud konfigurace potřebuje přihlašovací údaje, můžete přihlašovací údaje zahrnout do **protectedSettings**:

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

Následující příklad ukazuje výchozí chování rozšíření DSC, což znamená poskytnutí nastavení metadat pro funkci LCM a registraci ve službě Automatizace DSC.
Argumenty konfigurace jsou povinné.
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

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Příklad použití konfiguračního skriptu v Azure Storage

Následující příklad je z [přehledu obslužné rutiny rozšíření DSC](dsc-overview.md).
Tento příklad používá Správce prostředků šablon namísto rutin pro nasazení rozšíření.
Uložte konfiguraci IisInstall.ps1, umístěte ji do souboru. zip (příklad: `iisinstall.zip` ) a pak tento soubor nahrajte na adresu URL k dispozici.
Tento příklad využívá úložiště objektů BLOB v Azure, ale můžete si stáhnout soubory. zip z libovolného místa.

V šabloně Správce prostředků vydá následující kód virtuálnímu počítači, aby stáhl správný soubor, a pak spustí příslušnou funkci PowerShellu:

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

## <a name="example-using-referenced-azure-automation-registration-values"></a>Příklad použití odkazovaných hodnot registrace Azure Automation

Následující příklad načte **RegistrationUrl** a **RegistrationKey** a odkazuje na vlastnosti účtu Azure Automation a pomocí metody  **klíče listkey** načíst primární klíč (0).  V tomto příkladu byly do šablony zadány parametry **automationAccountName** a **NodeConfigName** .

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

Všechna nastavení v předchozím formátu rozšíření (a mají veřejné vlastnosti **ModulesUrl**, **ModuleSource**, **ModuleVersion**, **ConfigurationFunction**, **SasToken**nebo **vlastnosti**) se automaticky přizpůsobí aktuálnímu formátu rozšíření.
Pracují stejně jako dříve.

Následující schéma ukazuje, co vypadá jako u předchozího schématu nastavení:

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

Tady je postup, jak se v předchozím formátu přizpůsobí aktuálnímu formátu:

| Název aktuální vlastnosti | Předchozí ekvivalent schématu |
| --- | --- |
| Settings. wmfVersion |možnost. WMFVersion |
| settings.configuration. URL |možnost. ModulesUrl |
| settings.configuration. Script |První část settings.ConfigurationFunction (před \\ \\ ) |
| settings.configuration. Function |Druhá část settings.ConfigurationFunction (za \\ \\ ) |
| settings.configuration.module.name | možnost. ModuleSource |
| settings.configuration. Module. Version | možnost. ModuleVersion |
| settings.configurationArguments |možnost. Vlastnosti |
| settings.configurationData. URL |protectedSettings. DataBlobUri (bez tokenu SAS) |
| Settings. Privacy. DataCollection |možnost. Soukromí. DataCollection |
| Settings. advancedOptions. downloadMappings |možnost. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings. Properties |
| protectedSettings.configurationUrlSasToken |možnost. SasToken |
| protectedSettings.configurationDataUrlSasToken |Token SAS z protectedSettings. DataBlobUri |

## <a name="troubleshooting"></a>Řešení potíží

Tady jsou některé chyby, ke kterým může dojít, a jak je můžete opravit.

### <a name="invalid-values"></a>Neplatné hodnoty

"Ochrany osobních údajů. DataCollection je" {0} .
Jediné možné hodnoty jsou ' ', ' Enable ' a ' Disable '.
"WmfVersion je" {0} .
Pouze možné hodnoty jsou... a ' nejnovější '.

**Problém**: zadaná hodnota není povolená.

**Řešení**: Změňte neplatnou hodnotu na platnou hodnotu.
Další informace najdete v tabulce v [podrobnostech](#details).

### <a name="invalid-url"></a>Neplatná adresa URL

"ConfigurationData. URL je" {0} '. Toto není platná adresa URL "" DataBlobUri je ' {0} '. Toto není platná adresa URL "" konfigurace. adresa URL je ' {0} '. Toto není platná adresa URL. "

**Problém**: Zadaná adresa URL není platná.

**Řešení**: Ověřte zadané adresy URL.
Zajistěte, aby všechny adresy URL byly přeloženy na platná umístění, ke kterým může rozšíření přistupovat na vzdáleném počítači.

### <a name="invalid-registrationkey-type"></a>Neplatný typ RegistrationKey

"Neplatný typ pro parametr RegistrationKey typu PSCredential."

**Problém**: hodnota *RegistrationKey* v protectedSettings.configurationArguments nemůže být poskytnuta jako jiný typ než PSCredential.

**Řešení**: Změňte protectedSettings.configzáznam UrationArguments pro RegistrationKey na typ PSCredential v následujícím formátu:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Neplatný typ ConfigurationArgument

"Neplatný typ configurationArguments {0} "

**Problém**: vlastnost *ConfigurationArguments* nejde přeložit na objekt **zatřiďovací tabulky** .

**Řešení**: nastavte vlastnost *ConfigurationArguments* na **zatřiďovací tabulku**.
Postupujte podle formátu uvedeného v předchozích příkladech. Sledujte uvozovky, čárky a složené závorky.

### <a name="duplicate-configurationarguments"></a>Duplicitní ConfigurationArguments

"Našly se duplicitní argumenty {0} v Public i Protected configurationArguments".

**Problém**: *ConfigurationArguments* ve veřejných nastaveních a *ConfigurationArguments* v chráněných nastaveních mají vlastnosti se stejným názvem.

**Řešení**: Odeberte jednu z duplicitních vlastností.

### <a name="missing-properties"></a>Chybějící vlastnosti

settings.Configuration. Function vyžaduje, aby settings.configuration. URL nebo settings.configuration. Module je zadaný.

settings.Configuration. URL vyžaduje, aby byl zadán settings.configuration. Script.

"settings.Configuration. Script vyžaduje, aby byla zadána settings.configuration. URL"

settings.Configuration. URL vyžaduje, aby byla zadána settings.configuration. Function.

"protectedSettings.ConfigurationUrlSasToken vyžaduje settings.configuration. adresa URL je zadaná."

"protectedSettings.ConfigurationDataUrlSasToken vyžaduje settings.configurationData. adresa URL je zadaná."

**Problém**: definovaná vlastnost vyžaduje jinou vlastnost, která chybí.

**Řešení**:

- Zadejte chybějící vlastnost.
- Odeberte vlastnost, která potřebuje chybějící vlastnost.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [používání služby Virtual Machine Scale Sets s rozšířením Azure DSC](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Přečtěte si další podrobnosti o [správě zabezpečených přihlašovacích údajů DSC](dsc-credentials.md).
- Získejte [Úvod k obslužné rutině rozšíření Azure DSC](dsc-overview.md).
- Další informace o prostředí PowerShell DSC najdete v [centru dokumentace PowerShellu](/powershell/scripting/dsc/overview/overview).

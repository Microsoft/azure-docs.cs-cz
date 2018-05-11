---
title: Požadovaný stav konfigurace rozšíření s šablon Azure Resource Manageru
description: Další informace o definici šablony Resource Manageru pro rozšíření konfigurace požadovaného stavu (DSC) v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: d19d33d7d8d19923e814b8685c3e56553f85850d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Požadovaný stav konfigurace rozšíření s šablon Azure Resource Manageru

Tento článek popisuje šablony Azure Resource Manageru pro [obslužná rutina rozšíření konfigurace požadovaného stavu (DSC)](dsc-overview.md).

> [!NOTE]
> Může dojít k příklady mírně odlišné schématu. Ve verzi z října 2016 došlo ke změně ve schématu. Podrobnosti najdete v tématu [aktualizace z předchozí formátu](#update-from-the-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Příklad šablony pro virtuální počítač s Windows

Následující fragment kódu je třeba do **prostředků** část šablony.
Rozšíření DSC dědí vlastnosti rozšíření výchozí.
Další informace najdete v tématu [VirtualMachineExtension třída](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.).

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.76",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "protectedSettings": {
                "Items": {
                    "registrationKeyPrivate": "registrationKey"
                }
            },
            "publicSettings": {
                "configurationArguments": [{
                        "Name": "RegistrationKey",
                        "Value": {
                            "UserName": "PLACEHOLDER_DONOTUSE",
                            "Password": "PrivateSettingsRef:registrationKeyPrivate"
                        }
                    },
                    {
                        "RegistrationUrl": "registrationUrl",
                    },
                    {
                        "NodeConfigurationName": "nodeConfigurationName"
                    }
                ]
            }
        }
    }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Nastaví příkladu šablony pro škálování virtuálních počítačů Windows

Má uzel sady škálování virtuálního počítače **vlastnosti** oddíl, který má **VirtualMachineProfile extensionProfile** atribut.
V části **rozšíření**, přidat podrobnosti pro rozšíření DSC.

Rozšíření DSC dědí vlastnosti rozšíření výchozí.
Další informace najdete v tématu [VirtualMachineScaleSetExtension třída](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet).

```json
"extensionProfile": {
    "extensions": [{
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.Powershell",
            "type": "DSC",
            "typeHandlerVersion": "2.76",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "protectedSettings": {
                    "Items": {
                        "registrationKeyPrivate": "registrationKey"
                    }
                },
                "publicSettings": {
                    "configurationArguments": [{
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        {
                            "RegistrationUrl": "registrationUrl",
                        },
                        {
                            "NodeConfigurationName": "nodeConfigurationName"
                        }
                    ]
                }
            },
        }
    }]
}
```

## <a name="detailed-settings-information"></a>Informace o podrobné nastavení

Použít následující schéma v **nastavení** oddílu rozšíření Azure DSC v šabloně Resource Manager.

Seznam argumentů, které jsou k dispozici pro výchozí konfigurační skript najdete v tématu [výchozí konfigurační skript](#default-configuration-script).

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
| settings.wmfVersion |řetězec |Určuje verzi Windows Management Framework (WMF), který by měly být nainstalovány na vašem virtuálním počítači. Nastavení této vlastnosti na **nejnovější** nainstaluje nejnovější verzi WMF. V současné době jsou pouze možné hodnoty pro tuto vlastnost **4.0**, **5.0**, **5.0PP**, a **nejnovější**. Tyto hodnoty jsou předmětem aktualizace. Výchozí hodnota je **nejnovější**. |
| Settings.Configuration.URL |řetězec |Určuje adresu URL umístění, ze kterého chcete stáhnout soubor ZIP konfigurace DSC. Pokud zadaná adresa URL vyžaduje pro přístup k tokenu SAS, nastavte **protectedSettings.configurationUrlSasToken** vlastnost na hodnotu tokenu SAS. Tato vlastnost je vyžadována, pokud **settings.configuration.script** nebo **settings.configuration.function** jsou definovány. Pokud pro tyto vlastnosti je zadána žádná hodnota, rozšíření volá výchozí konfigurační skript k nastavení metadat umístění Configuration Manager (LCM) a musí být zadán argument. |
| Settings.Configuration.Script |řetězec |Určuje název souboru skriptu, který obsahuje definici konfigurace DSC. Tento skript musí být v kořenové složce souboru .zip, který byl stažen z adresa URL zadaná **configuration.url** vlastnost. Tato vlastnost je vyžadována, pokud **settings.configuration.url** nebo **settings.configuration.script** jsou definovány. Pokud pro tyto vlastnosti je zadána žádná hodnota, rozšíření volá výchozí konfigurační skript nastavit LCM metadat a musí být zadán argument. |
| Settings.Configuration.Function |řetězec |Určuje název konfigurace DSC. Konfigurace, který je pojmenován musí být součástí skriptu, **configuration.script** definuje. Tato vlastnost je vyžadována, pokud **settings.configuration.url** nebo **settings.configuration.function** jsou definovány. Pokud pro tyto vlastnosti je zadána žádná hodnota, rozšíření volá výchozí konfigurační skript nastavit LCM metadat a musí být zadán argument. |
| settings.configurationArguments |Kolekce |Definuje žádné parametry, které chcete předat do vaší konfigurace DSC. Tato vlastnost není zašifrován. |
| settings.configurationData.url |řetězec |Určuje adresu URL, ze kterého mají být staženy konfiguračního souboru dat (.psd1) chcete použít jako vstup pro konfiguraci DSC. Pokud zadaná adresa URL vyžaduje pro přístup k tokenu SAS, nastavte **protectedSettings.configurationDataUrlSasToken** vlastnost na hodnotu tokenu SAS. |
| settings.privacy.dataEnabled |řetězec |Povolí nebo zakáže telemetrie kolekce. Pro tuto vlastnost pouze možné hodnoty jsou **povolit**, **zakázat**, **s**, nebo **$null**. Umožňuje telemetrie, a tato vlastnost prázdná nebo mít hodnotu null. Výchozí hodnota je **s**. Další informace najdete v tématu [shromažďování dat rozšíření Azure DSC](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/). |
| settings.advancedOptions.downloadMappings |Kolekce |Určuje alternativní umístění, ze kterého mají být staženy WMF. Další informace najdete v tématu [rozšíření Azure DSC 2.8 a způsob namapování stahování závislosti rozšíření do vlastního umístění](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx). |
| protectedSettings.configurationArguments |Kolekce |Definuje žádné parametry, které chcete předat do vaší konfigurace DSC. Tato vlastnost je zašifrován. |
| protectedSettings.configurationUrlSasToken |řetězec |Určuje token SAS, který má používat pro přístup k adresu URL, **configuration.url** definuje. Tato vlastnost je zašifrován. |
| protectedSettings.configurationDataUrlSasToken |řetězec |Určuje token SAS, který má používat pro přístup k adresu URL, **configurationData.url** definuje. Tato vlastnost je zašifrován. |

## <a name="default-configuration-script"></a>Výchozí konfigurační skript

Další informace o těchto hodnot najdete v tématu [základní nastavení správce místní konfigurace](/powershell/dsc/metaconfig#basic-settings).
Skript DSC rozšíření výchozí konfigurace můžete použít ke konfiguraci pouze LCM vlastnosti, které jsou uvedeny v následující tabulce.

| Název vlastnosti | Typ | Popis |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |SecureString |Požadovaná vlastnost. Určuje klíč, který se použije pro uzel k registraci ve službě Azure Automation jako heslo objekt přihlašovacích údajů prostředí PowerShell. Tato hodnota může automaticky zjistit pomocí **listkeys** metoda proti účet Automation. Hodnota musí být zabezpečený jako chráněný nastavení. |
| settings.configurationArguments.RegistrationUrl |řetězec |Požadovaná vlastnost. Určuje adresu URL koncového bodu Automation, kde pokusí registraci uzlu. Tato hodnota může automaticky zjistit pomocí **odkaz** metoda proti účet Automation. |
| settings.configurationArguments.NodeConfigurationName |řetězec |Požadovaná vlastnost. Určuje konfigurace uzlu v účtu Automation přiřadit k uzlu. |
| settings.configurationArguments.ConfigurationMode |řetězec |Určuje režim pro LCM. Platné možnosti zahrnují **ApplyOnly**, **ApplyandMonitor**, a **ApplyandAutoCorrect**.  Výchozí hodnota je **ApplyandMonitor**. |
| settings.configurationArguments.RefreshFrequencyMins | UInt32 | Určuje, jak často se pokusí LCM obraťte se na účtu Automation aktualizací.  Výchozí hodnota je **30**.  Minimální hodnota je **15**. |
| settings.configurationArguments.ConfigurationModeFrequencyMins | UInt32 | Určuje, jak často LCM ověří aktuální konfiguraci. Výchozí hodnota je **15**. Minimální hodnota je **15**. |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | Určuje, zda uzel může automaticky restartovat, pokud operace DSC požaduje. Výchozí hodnota je **false**. |
| settings.configurationArguments.ActionAfterReboot | řetězec | Určuje, co se stane po restartování systému při aplikování konfigurace. Platné možnosti jsou **ContinueConfiguration** a **StopConfiguration**. Výchozí hodnota je **ContinueConfiguration**. |
| settings.configurationArguments.AllowModuleOverwrite | boolean | Určuje, zda LCM přepíše existující modulů na uzlu. Výchozí hodnota je **false**. |

## <a name="settings-vs-protectedsettings"></a>Nastavení vs. ProtectedSettings

Všechna nastavení se ukládají do textového souboru nastavení ve virtuálním počítači.
Vlastnosti, které jsou uvedené v části **nastavení** jsou veřejné vlastnosti.
Veřejné vlastnosti nejsou šifrovány nastavení textového souboru.
Vlastnosti, které jsou uvedené v části **protectedSettings** jsou šifrované pomocí certifikátu a nejsou zobrazeny ve formátu prostého textu v souboru nastavení ve virtuálním počítači.

Pokud konfigurace potřebuje přihlašovací údaje, můžete zahrnout přihlašovací údaje v **protectedSettings**:

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

## <a name="example-configuration-script"></a>Příklad konfigurační skript

Následující příklad ukazuje, výchozí chování v rozšíření DSC, což je poskytovat LCM nastavení metadat a registrace ve službě Automation DSC.
Konfigurace argumenty jsou povinné.
Výchozí konfigurační skript nastavit LCM metadata jsou předáno konfigurace argumentů.

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl": "[parameters('registrationUrl1')]",
        "NodeConfigurationName": "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "Items": {
        "registrationKeyPrivate": "[parameters('registrationKey1']"
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Příklad použití konfigurační skript ve službě Azure Storage

Následující příklad je z [DSC rozšíření obslužné rutiny přehled](dsc-overview.md).
Tento příklad používá šablony Resource Manager místo rutiny pro nasazení rozšíření.
Uložte konfiguraci IisInstall.ps1, umístěte jej do souboru ZIP a potom odeslat soubor na adresu URL přístupné.
Tento příklad používá úložiště objektů Blob v Azure, ale soubory .zip si můžete stáhnout z libovolného libovolného umístění.

Následující kód v šabloně Resource Manager dává pokyn virtuálního počítače ke stažení správný soubor a pak spusťte odpovídající funkce prostředí PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="update-from-a-previous-format"></a>Aktualizace z předchozí formátu

Všechna nastavení ve formátu předchozí rozšíření (a které mají veřejné vlastnosti **ModulesUrl**, **ConfigurationFunction**, **SasToken**, nebo  **Vlastnosti**) automaticky přizpůsobit na současný formát rozšíření.
Spouštějí se stejně jako před.

Následující schéma ukazuje, jaké předchozí nastavení schématu hledá jako:

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

Zde je, jak se v předchozím formátu přizpůsobuje na současný formát:

| Název vlastnosti | Předchozí schématu ekvivalentní |
| --- | --- |
| settings.wmfVersion |nastavení. WMFVersion |
| Settings.Configuration.URL |settings.ModulesUrl |
| Settings.Configuration.Script |První část nastavení. ConfigurationFunction (před \\ \\) |
| Settings.Configuration.Function |Druhá část nastavení. ConfigurationFunction (po \\ \\) |
| settings.configurationArguments |nastavení. Vlastnosti |
| settings.configurationData.url |protectedSettings.DataBlobUri (bez tokenu SAS) |
| settings.privacy.dataEnabled |settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings |nastavení. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |Token SAS z protectedSettings.DataBlobUri |

## <a name="troubleshooting---error-code-1100"></a>Řešení potíží – kód chyby 1100

Kód chyby 1100 znamená problém s vstupu uživatele na rozšíření DSC.
Text tyto chyby se liší a mohou změnit.
Zde jsou některé z chyb, které můžete narazit na a jak můžete opravit je.

### <a name="invalid-values"></a>Neplatné hodnoty

"Je Privacy.dataCollection '{0}'.
Pouze možné hodnoty jsou ","Zapnout"a"Zakázat"".
"Je WmfVersion '{0}'.
Pouze možné hodnoty jsou... a 'nejnovější' ".

**Problém**: Zadaná hodnota není povolena.

**Řešení**: Neplatná hodnota změňte na platnou hodnotu.
Další informace najdete v tabulce v [podrobnosti](#details).

### <a name="invalid-url"></a>Neplatná adresa URL

"Je ConfigurationData.url '{0}'. Toto není platná adresa URL."" je DataBlobUri '{0}'. Toto není platná adresa URL."" je Configuration.url '{0}'. Toto není platná adresa URL."

**Problém**: A Zadaná adresa URL není platná.

**Řešení**: Zkontrolujte všechny zadané URL.
Zajistěte, že všechny adresy URL přeložit na platné umístění, můžete přístup k rozšíření ve vzdáleném počítači.

### <a name="invalid-configurationargument-type"></a>Neplatný typ ConfigurationArgument

"Neplatný configurationArguments typ {0}"

**Problém**: *ConfigurationArguments* vlastnost nemůže být vyhodnocena **zatřiďovací tabulky** objektu.

**Řešení**: Zkontrolujte vaše *ConfigurationArguments* vlastnost **zatřiďovací tabulky**.
Použijte formát najdete v předchozím příkladu. Podívejte se na nabídky, čárky a složené závorky.

### <a name="duplicate-configurationarguments"></a>Duplicitní ConfigurationArguments

"Nalezena duplicitní argumenty{0}' ve veřejných i chráněný configurationArguments"

**Problém**: *ConfigurationArguments* v nastavení veřejných a *ConfigurationArguments* v chráněných nastavení mít vlastnosti se stejným názvem.

**Řešení**: odeberte jeden z duplicitní vlastnosti.

### <a name="missing-properties"></a>Chybí vlastnosti

"Configuration.function vyžaduje, aby byl zadán configuration.url nebo configuration.module"

"Configuration.url vyžaduje, aby byl že tento configuration.script je zadán"

"Configuration.script vyžaduje, aby byl že tento configuration.url je zadán"

"Configuration.url vyžaduje, aby byl že tento configuration.function je zadán"

"ConfigurationUrlSasToken vyžaduje, aby byl že tento configuration.url je zadán"

"ConfigurationDataUrlSasToken vyžaduje, aby byl že tento configurationData.url je zadán"

**Problém**: definované vlastnosti potřebuje další vlastnosti, která nebyla nalezena.

**Řešení**:

- Zadejte chybějící vlastnost.
- Odeberte vlastnost, která potřebuje chybí vlastnost.

## <a name="next-steps"></a>Další postup

- Další informace o [použití škálování virtuálních počítačů sad s příponou Azure DSC](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md).
- Najít další podrobnosti o [správu zabezpečení přihlašovacích údajů DSC](dsc-credentials.md).
- Získat [Úvod do rozšíření obslužné rutiny Azure DSC](dsc-overview.md).
- Další informace o DSC Powershellu, přejděte na [centru dokumentace prostředí PowerShell](/powershell/dsc/overview).

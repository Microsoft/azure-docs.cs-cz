---
title: Řešení potíží s Azure Automation požadovaného stavu konfigurace (DSC)
description: Tento článek obsahuje informace o řešení potíží s konfigurace požadovaného stavu (DSC)
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5d2eae67fcff74a7016f7f6125e31a9c8c2bda97
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063825"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Řešení potíží s konfigurace požadovaného stavu (DSC)

Tento článek obsahuje informace o řešení potíží s potřeby konfigurace stavu (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Běžné chyby při práci s potřeby konfigurace stavu (DSC)

### <a name="failed-not-found"></a>Scénář: Uzel je ve stavu selhání s chybou "Nebyl nalezen."

#### <a name="issue"></a>Problém

Uzel obsahuje sestavu s **se nezdařilo** stavu a který obsahuje chybu:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Příčina

Této chybě obvykle dochází při uzlu je přiřazen název konfigurace (například ABC) namísto název konfigurace uzlu (například ABC. Webový server).

#### <a name="resolution"></a>Řešení

* Ujistěte se, že přiřazujete uzlu se "název konfigurace uzlu" a ne "konfigurace název".
* Konfigurace uzlu můžete přiřadit k uzlu, pomocí portálu Azure nebo pomocí rutiny prostředí PowerShell.

  * Aby bylo možné přiřadit konfigurace uzlu do uzlu pomocí portálu Azure, otevřete **uzly DSC** stránky, pak vyberte uzel a klikněte na **konfigurace uzlu přiřazení** tlačítko.  
  * Aby bylo možné přiřadit konfigurace uzlu do uzlu pomocí rutiny prostředí PowerShell, použijte **Set-AzureRmAutomationDscNode** rutiny

### <a name="no-mof-files"></a>Scénář: Žádná konfigurace uzlu (soubory MOF) byly vytvořeny při kompilaci konfigurace

#### <a name="issue"></a>Problém

Pozastaví úlohu kompilace DSC došlo k chybě:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Příčina

Pokud následující výraz **uzlu** – klíčové slovo v konfigurace DSC se vyhodnocuje `$null`, pak vytváří se žádné konfigurace uzlů.

#### <a name="resolution"></a>Řešení

Problém opravte některý z následujících řešení:

* Ujistěte se, že výraz vedle **uzlu** – klíčové slovo v definici konfigurace není vyhodnocení na $null.
* Pokud předáváte ConfigurationData při kompilaci konfigurace, ujistěte se, že předáváte očekávaných hodnot, které konfigurace vyžaduje, aby [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scénář: Sestavy uzlu DSC stane zablokované stav "v průběhu"

#### <a name="issue"></a>Problém

Výstupy agenta DSC:

```
No instance found with given property values
```

#### <a name="cause"></a>Příčina

Upgradu vaší verzi WMF a mají poškozen rozhraní WMI.

#### <a name="resolution"></a>Řešení

O vyřešení problému postupujte podle pokynů v [DSC známé problémy a omezení](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) článku.

### <a name="issue-using-credential"></a>Scénář: Nelze použít pověření v konfigurace DSC

#### <a name="issue"></a>Problém

Úlohu kompilace DSC byla pozastavena kvůli chybě:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Příčina

Jste použili pověření v konfiguraci, ale neposkytli správné **ConfigurationData** nastavit **PSDscAllowPlainTextPassword** na hodnotu true pro každý konfigurace uzlu.

#### <a name="resolution"></a>Řešení

* Ujistěte se, zda jste předat správnou **ConfigurationData** nastavit **PSDscAllowPlainTextPassword** na hodnotu true pro každý uzel, konfigurace uvedené v konfiguraci. Další informace najdete v tématu [prostředky v Azure Automation DSC](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Další postup

Pokud váš problém nenalezli nebo nemůžete vyřešit problém, navštíví některý z následujících kanály pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
---
title: Řešení potíží s Azure Automation Desired State Configuration (DSC)
description: Tento článek obsahuje informace o řešení potíží s Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 997f332e14fd1accf32d8cc3f51557fe005acab5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421638"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Řešení potíží s Desired State Configuration (DSC)

Tento článek obsahuje informace o řešení potíží s Desired State Configuration (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Běžné chyby při práci s Desired State Configuration (DSC)

### <a name="failed-not-found"></a>Scénář: Uzel je ve stavu selhání s chybou "Nebyl nalezen"

#### <a name="issue"></a>Problém

Uzel má sestava s **neúspěšné** stavu a který obsahuje chybu:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Příčina

K této chybě obvykle dochází, když uzlu je přiřazen název konfigurace (například ABC) namísto název konfigurace uzlu (například ABC. Webový server).

#### <a name="resolution"></a>Řešení

* Ujistěte se, že přiřazujete uzel s "název konfigurace uzlu" a ne "konfigurace název".
* Konfigurace uzlu můžete přiřadit k uzlu pomocí webu Azure portal nebo pomocí rutiny Powershellu.

  * Pokud chcete přiřadit konfiguraci uzlu k uzlu pomocí webu Azure portal, otevřete **uzly DSC** stránce, pak vyberte uzel a klikněte na **přiřadit konfiguraci uzlu** tlačítko.  
  * Chcete-li přiřadit konfiguraci uzlu k uzlu pomocí rutiny Powershellu, použijte **Set-AzureRmAutomationDscNode** rutiny

### <a name="no-mof-files"></a>Scénář: Žádná konfigurace uzlu (soubory MOF) byly vytvořeny při kompilaci konfigurace

#### <a name="issue"></a>Problém

Pozastaví úlohu kompilace DSC a došlo k chybě:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Příčina

Při následující výraz **uzel** vyhodnotí jako – klíčové slovo v konfiguraci DSC `$null`, pak se budou vytvářet žádné konfigurace uzlů.

#### <a name="resolution"></a>Řešení

Některé z následujících řešení tento problém vyřešit:

* Ujistěte se, že výraz vedle **uzel** – klíčové slovo v definici konfigurace se vyhodnotí na $null.
* Pokud předáváte ConfigurationData při kompilaci konfigurace, ujistěte se, že předáváte očekávané hodnoty, které vyžaduje konfiguraci z [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scénář: Sestavy uzlu DSC bude zablokované stavu "Probíhá"

#### <a name="issue"></a>Problém

Výstupy agenta DSC:

```
No instance found with given property values
```

#### <a name="cause"></a>Příčina

Upgradovaly verzi WMF a WMI dojít k poškození.

#### <a name="resolution"></a>Řešení

K vyřešení problému postupujte podle pokynů [DSC – známé problémy a omezení](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) článku.

### <a name="issue-using-credential"></a>Scénář: Nelze použít pověření v konfiguraci DSC

#### <a name="issue"></a>Problém

Úlohy kompilace DSC byla pozastavena kvůli chybě:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Příčina

Přihlašovací údaj jste použili v konfiguraci, ale neposkytli správné **ConfigurationData** nastavit **PSDscAllowPlainTextPassword** na hodnotu true pro každou konfiguraci uzlu.

#### <a name="resolution"></a>Řešení

* Ujistěte se, že a zajistěte tak předání správné **ConfigurationData** nastavit **PSDscAllowPlainTextPassword** na hodnotu true pro každou konfiguraci uzlu uvedená v konfiguraci. Další informace najdete v tématu [prostředky v Azure Automation DSC](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Další postup

Pokud nenalezli váš problém nebo nepovedlo se vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

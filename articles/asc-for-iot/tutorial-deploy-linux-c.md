---
title: Kurz k instalaci a nasazení agenta pro Linux C z ASC pro IoT agenta ve verzi Preview | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat ASC pro agenta IoT na 32bitová i 64bitová verze systému Linux.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 04b41791915946820c9c7a1666ab10e880731e4b
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541552"
---
# <a name="tutorial-deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Kurz: Nasazení ASC pro zabezpečení na základě IoT C agenta pro Linux

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento kurz vysvětluje, jak nainstalovat a nasadit ASC pro agenta zabezpečení na základě IoT C v Linuxu.

V tomto kurzu se naučíte: 
> [!div class="checklist"]
> * Instalace
> * Ověření nasazení
> * Odinstalace agenta
> * Řešení potíží 

## <a name="prerequisites"></a>Požadavky

Další platformy a chuti agenta, najdete v části [vyberte správné zabezpečení agenta](select-deploy-agent.md).

1. Nasaďte agenta nástroje zabezpečení, se vyžadují oprávnění místního správce na počítači, který chcete nainstalovat.

1. [Vytvoření modulu zabezpečení](quickstart-create-security-twin.md) zařízení.

## <a name="installation"></a>Instalace 

Při instalaci a nasazení agenta zabezpečení, postupujte takto:

1. Stáhnout nejnovější verzi na svůj počítač z [Githubu](https://aka.ms/iot-security-github-cs).

1. Extrahujte obsah balíčku a přejděte _/installation_ složky.

1. Přidat spuštěné oprávnění **InstallSecurityAgent skript** spuštěním následujícího:
    
    `chmod +x InstallSecurityAgent.sh` 

1. Potom spusťte: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Zobrazit [ke konfiguraci ověřování](concept-security-agent-authentication-methods.md) Další informace o ověření parametrů.

Skript provede následující akce:

1. Nainstaluje požadavky.

2. Přidá uživatele služby (pomocí interaktivního přihlášení, zakázáno).

3. Nainstaluje agenta jako **démon** – předpokládá, zařízení používá **systemd** pro správu služeb.

4. Nakonfiguruje **sudoers** smí agent k provádění určitých úloh jako uživatel root.

5. Nakonfiguruje agenta s parametry ověřování k dispozici. 

Potřebujete další pomoc, spuštěním skriptu s parametrem – nápovědy: 
    
    `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Odinstalace agenta, spusťte skript s parametrem – u:

    `./InstallSecurityAgent.sh -u`. 

```
 .\InstallSecurityAgent.sh –uninstall / -u
``` 

## <a name="troubleshooting"></a>Řešení potíží
Spuštěním kontroly stavu nasazení:

```
systemctl status ASCIoTAgent.service
```


## <a name="next-steps"></a>Další postup
- Přečtěte si ASC pro služby Azure IoT [– přehled](overview.md)
- Další informace o ASC pro IoT [architektury](architecture.md)
- Povolit [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [– nejčastější dotazy](resources-frequently-asked-questions.md)
- Vysvětlení [výstrahy zabezpečení](concept-security-alerts.md)
---
title: Pokyny k instalaci a nasazení agenta pro Linux C služby Azure Security Center pro IoT agenta ve verzi Preview | Dokumentace Microsoftu
description: Zjistěte, jak Azure Security Center pro IoT agenta nainstalovat na 32bitová verze a 64bitová verze systému Linux.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 49ebb5932e1e918330625fd0df98811873dd5cd5
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200658"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Nasazení Azure Security Center pro zabezpečení na základě IoT C agenta pro Linux

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tato příručka vysvětluje, jak nainstalovat a nasadit Azure Security Center (ASC) pro zabezpečení na základě IoT C agenta v Linuxu.

V této příručce se naučíte: 
> [!div class="checklist"]
> * Instalace
> * Ověření nasazení
> * Odinstalace agenta
> * Řešení potíží 

## <a name="prerequisites"></a>Požadavky

Další platformy a typů agenta, najdete v části [vyberte správné zabezpečení agenta](how-to-deploy-agent.md).

1. Pokud chcete nasadit agenta zabezpečení, se vyžadují práva místního správce na počítači, na kterém chcete nainstalovat na (sudo).

1. [Vytvoření modulu zabezpečení](quickstart-create-security-twin.md) zařízení.

## <a name="installation"></a>Instalace 

Při instalaci a nasazení agenta zabezpečení, postupujte takto:


1. Stáhnout nejnovější verzi na svůj počítač z [Githubu](https://aka.ms/iot-security-github-c).

1. Extrahujte obsah balíčku a přejděte _/Install_ složky.

1. Přidat spuštěné oprávnění **InstallSecurityAgent skript** spuštěním následujícího:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Potom spusťte: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Zobrazit [ke konfiguraci ověřování](concept-security-agent-authentication-methods.md) Další informace o ověření parametrů.

Skript provede následující akce:

1. Nainstaluje požadavky.

2. Přidá uživatele služby (pomocí interaktivního přihlášení, zakázáno).

3. Nainstaluje agenta jako **démon** – předpokládá, zařízení používá **systemd** pro správu služeb.

4. Nakonfiguruje agenta s parametry ověřování k dispozici. 

Potřebujete další pomoc, spuštěním skriptu s parametrem – nápovědy: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Odinstalace agenta, spusťte skript s – – odinstalovat parametr:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Řešení potíží
Spuštěním kontroly stavu nasazení:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Další postup
- Přečtěte si ASC pro služby Azure IoT [– přehled](overview.md)
- Další informace o ASC pro IoT [architektury](architecture.md)
- Povolit [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [– nejčastější dotazy](resources-frequently-asked-questions.md)
- Vysvětlení [výstrahy zabezpečení](concept-security-alerts.md)
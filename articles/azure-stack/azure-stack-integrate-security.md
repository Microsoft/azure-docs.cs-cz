---
title: Integrace datových center Azure Stack – zabezpečení
description: Zjistěte, jak integrovat zabezpečení vašeho datového centra zabezpečení Azure stacku
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 9f356b814ac1ac6ca8b6d6efe7cb9f5d9ed66270
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442471"
---
# <a name="azure-stack-datacenter-integration---security"></a>Integrace datových center Azure Stack – zabezpečení
Azure Stack byl navržen a sestaven s ohledem na bezpečnost. Azure Stack je uzamknuté systému, takže nepodporuje instalaci agenta zabezpečení softwaru.

Tento článek vám umožňuje integraci funkcí zabezpečení služby Azure Stack s řešení zabezpečení, které jsou už nasazené ve vašem datovém centru.

## <a name="security-logs"></a>Protokoly zabezpečení

Azure Stack shromažďuje operačního systému a události zabezpečení pro infrastrukturu role a škálování jednotek uzlů každé dvě minuty. Protokoly se ukládají v kontejnerech objektů blob v účtu úložiště.

Existuje jeden účet úložiště na roli serveru infrastruktury a jeden obecný účet úložiště pro všechny události typického operačního systému.

Stav poskytovatele prostředků je možné volat prostřednictvím protokolu REST se načíst adresu URL do kontejneru objektů blob. Řešení zabezpečení jiných výrobců můžete použít k načtení událostí ke zpracování účty rozhraní API a úložiště.

### <a name="use-azure-storage-explorer-to-view-events"></a>Zobrazit události pomocí Průzkumníka služby Azure Storage

Můžete načíst události shromážděné nástroj zvaný Průzkumníka služby Azure Storage pomocí služby Azure Stack. Můžete si stáhnout Průzkumníka služby Azure Storage z [ http://storageexplorer.com ](http://storageexplorer.com).

Následující postup je příklad, který můžete použít ke konfiguraci Průzkumníka služby Azure Storage pro Azure Stack:

1. Přihlaste se k portálu Správce služby Azure Stack jako operátor.
1. Procházet **účty úložiště** a hledejte **frphealthaccount**. **Frphealthaccount** účet obecného úložiště pro ukládání všech událostí operačního systému.

   ![Účty úložiště](media/azure-stack-integrate-security/storage-accounts.png)

1. Vyberte **frphealthaccount**, pak klikněte na tlačítko **přístupové klíče**.

   ![Přístupové klíče](media/azure-stack-integrate-security/access-keys.png)

1. Přístupový klíč zkopírujte do schránky.
1. Otevřete Průzkumníka služby Azure Storage.
1. Na **upravit** nabídce vyberte možnost **Target Azure Stack**.
1. Vyberte **přidat účet**a pak vyberte **použít název účtu úložiště a klíč**.

   ![Připojit úložiště](media/azure-stack-integrate-security/connect-storage.png)

1. Klikněte na **Další**.
1. Na **připojit externí úložiště** stránky:

   a. Zadejte název účtu **frphealthaccount**.

   b. Vložte přístupový klíč účtu úložiště.

   c. V části **úložiště koncové body domény**, vyberte **jiných**a zadejte koncový bod úložiště **[Oblast]. [ DomainName]**.

   d. Vyberte **použít protokol HTTP** zaškrtávací políčko.

   ![Připojit externí úložiště](media/azure-stack-integrate-security/attach-storage.png)

1. Klikněte na tlačítko **Další**, zkontrolujte souhrn a **Dokončit** průvodce.
1. Teď můžete procházet kontejnery objektů blob v jednotlivých a stahovat události.

   ![Procházet objekty BLOB](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Pomocí programovacích jazyků k přístupu k událostem

Pro přístup k účtu úložiště můžete použít různé programovací jazyky. Vyberte si příklad, který odpovídá jazyku pomocí následující dokumentaci:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Auditování přístupu k zařízení

Všechny fyzické zařízení ve službě Azure Stack podporují použití TACACS nebo pomocí protokolu RADIUS. To zahrnuje přístup k řadiči pro správu základní desky (BMC) a síťové přepínače.

Řešení Azure Stack se nedodává s protokolem RADIUS nebo TACACS součástí. Ale řešení ověřily pro podporu použití existující RADIUS nebo TACACS řešení na trhu.

Pro RADIUS pouze MSCHAPv2 potvrzená. To představuje nejbezpečnější implementace pomocí protokolu RADIUS.
Poraďte se s OEM dodavatele hardwaru pro povolení TACAS nebo pomocí protokolu RADIUS v zařízení zahrnutých v rámci řešení pro Azure Stack.

## <a name="syslog"></a>Syslog

Všechny fyzické zařízení ve službě Azure Stack odesílat zprávy Syslog. Řešení Azure Stack se nedodává s Syslog server. Ale řešení ověřily pro podporu odesílání zpráv do existujícího řešení Syslog na trhu.

Cílová adresa Syslog je volitelný parametr shromážděných pro nasazení, ale také přidáním po nasazení.

## <a name="next-steps"></a>Další postup

[Zásady údržby](azure-stack-servicing-policy.md)

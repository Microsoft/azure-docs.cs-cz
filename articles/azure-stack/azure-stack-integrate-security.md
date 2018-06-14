---
title: Zásobník datacenter integrace se službou Azure - zabezpečení
description: Zjistěte, jak integrovat Azure zásobníku zabezpečení zabezpečení vašeho datového centra
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 8ce9045a3e4fd12d61e9b1600ee98880762bc544
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29734423"
---
# <a name="azure-stack-datacenter-integration---security"></a>Zásobník datacenter integrace se službou Azure - zabezpečení
Azure zásobníku byl navržen a sestaven s důrazem na bezpečnost. Azure zásobníku je uzamčené systém, proto není podporována instalace agenta zabezpečení softwaru.

Tento článek vám umožňuje integrovat řešení zabezpečení, už nasazená ve vašem datovém centru Azure zásobníku funkce zabezpečení.

## <a name="security-logs"></a>Protokoly zabezpečení

Azure zásobníku shromažďuje operačního systému a události zabezpečení pro infrastrukturu role a uzly jednotky škálování každé dvě minuty. Protokoly jsou uloženy v kontejnerech objektů blob účet úložiště.

Je jeden účet úložiště na role infrastruktury a jeden účet obecné úložiště pro všechny události typické operačního systému.

Poskytovatel stavu prostředků je možné volat prostřednictvím protokolu REST pro načtení adresy URL do kontejneru objektů blob. Řešení třetí strany zabezpečení můžete použít rozhraní API a úložiště účty k načtení události ke zpracování.

### <a name="use-azure-storage-explorer-to-view-events"></a>Zobrazení událostí pomocí Průzkumníka úložiště Azure

Můžete načíst událostí shromážděných za zásobník Azure pomocí nástroje Azure Storage Explorer volat. Můžete si stáhnout Azure Storage Explorer z [http://storageexplorer.com](http://storageexplorer.com).

Následující postup je příklad, které můžete použít ke konfiguraci Azure Storage Explorer pro Azure zásobníku:

1. Přihlaste se k portálu správce Azure zásobníku jako operátor.
2. Procházet **účty úložiště** a vyhledejte **frphealthaccount**. **Frphealthaccount** účet je účet obecné úložiště používat k ukládání všech událostí operačního systému.

   ![Účty úložiště](media/azure-stack-integrate-security/storage-accounts.png)

3. Vyberte **frphealthaccount**, pak klikněte na tlačítko **přístupové klíče**.

   ![Přístupové klíče](media/azure-stack-integrate-security/access-keys.png)

4. Přístupový klíč zkopírujte do schránky.
5. Otevřete Průzkumníka úložiště Azure.
6. Na **upravit** nabídce vyberte možnost **zásobník Azure cíl**.
7. Vyberte **přidat účet**a potom vyberte **použít název účtu úložiště a klíč**.

   ![Připojení úložiště](media/azure-stack-integrate-security/connect-storage.png)

8. Klikněte na **Další**.
9. Na **připojit externí úložiště** stránky:

   a. Zadejte název účtu **frphealthaccount**.

   b. Vložte přístupový klíč účtu úložiště.

   c. V části **úložiště koncové body domény**, vyberte **jiných**a zadejte koncový bod úložiště **[Oblast]. [ DomainName]**.

   d. Vyberte **použít protokol HTTP** zaškrtávací políčko.

   ![Připojit externí úložiště](media/azure-stack-integrate-security/attach-storage.png)

10. Klikněte na tlačítko **Další**, zkontrolujte souhrn a **Dokončit** průvodce.
11. Teď můžete procházet kontejnery jednotlivých objektů blob a stáhnout události.

   ![Procházet objekty BLOB](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Použít programovacích jazyků k události přístupu.

Pro přístup k účtu úložiště můžete použít různé programovací jazyky. Použijte následující dokumentace a vyberte příklad, který odpovídá jazyku:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Auditování přístupu k zařízení

Všechny fyzické zařízení v Azure zásobníku podporují použití TACACS nebo RADIUS. Poskytuje přístup k řadiči pro správu základní desky (BMC) a síťové přepínače.

Řešení Azure zásobníku se nedodává s protokolem RADIUS nebo TACACS součástí. Však řešení ověření pro podporu použití existující RADIUS nebo TACACS řešení, k dispozici na trhu.

Pro RADIUS pouze MSCHAPv2 potvrzená. To představuje nejbezpečnější implementaci pomocí protokolu RADIUS.
Požádejte dodavatele hardwaru, od výrobců OEM pro povolení TACAS nebo protokolu RADIUS v zařízení je součástí řešení Azure zásobníku.

## <a name="syslog"></a>Syslog

Všechny fyzické zařízení v Azure zásobníku mohou zasílat zprávy Syslog. Řešení Azure zásobníku se nedodává s Syslog server. Však řešení ověření pro podporu odesílání zpráv do stávajících řešení Syslog dostupná na trhu.

Cílová adresa Syslog je volitelný parametr shromážděných pro nasazení, ale také přidáním nasazení post.

## <a name="next-steps"></a>Další postup

[Zásady údržby](azure-stack-servicing-policy.md)

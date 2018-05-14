---
title: Zálohování a data obnovení pro zásobník Azure pomocí služby Backup infrastruktury | Microsoft Docs
description: Můžete zálohovat a obnovit konfiguraci a data služby pomocí infrastruktury služby zálohování.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 8c8037fe3936485082299250e603b2f3ea3859b9
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Zálohování a data obnovení pro zásobník Azure pomocí služby Backup infrastruktury

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete zálohovat a obnovit konfiguraci a data služby pomocí infrastruktury služby zálohování. Instalace jednotlivých zásobník Azure obsahuje instance služby. Zálohy vytvořené pomocí služby pro opakované nasazení cloudu zásobník Azure můžete použít k obnovení identitu, zabezpečení a správce prostředků Azure data.

Až budete připraveni k uvedení do produkčního prostředí svém cloudu můžete povolit zálohování. Nepovolujte zálohování, pokud máte v plánu provést testování a ověření na dlouhou dobu.

Před povolením služby zálohování, zajistěte, aby byla [požadavky na místě](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Služba zálohování infrastruktury nezahrnuje aplikací a dat uživatele. Najdete v následujících článcích pokyny o zálohování a obnovení [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), a [MySQL](https://aka.ms/azure-stack-mysql) zprostředkovatelé prostředků a dat uživatele...

## <a name="the-infrastructure-backup-service"></a>Služby infrastruktura zálohování

Služba obsahuje následující funkce.

| Funkce                                            | Popis                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Služby infrastruktury zálohování                     | Koordinaci zálohování napříč podmnožinu služby infrastruktury v zásobníku Azure. Pokud dojde k havárii, lze data obnovit jako součást nového nasazení. |
| Komprese a šifrování exportovaný zálohování dat | Zálohovaná data komprimovaná a šifrovaná systém předtím, než se exportují do externího úložiště umístění poskytnutý správcem.                |
| Úloha zálohování monitorování                              | Systém upozorní, když selže a náprava kroky úlohy zálohování.                                                                                                |
| Zálohování prostředí                       | Zálohování RP podporuje povolení zálohování.                                                                                                                         |
| Obnovení cloudu                                     | Při závažné ztrátě dat zálohování můžete použít k obnovení základní informace zásobníku Azure jako součást nasazení.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Ověřte požadavky pro službu zálohování infrastruktury

- **Umístění úložiště**  
  Budete potřebovat sdílené složky přístupné z Azure zásobníku, která může obsahovat sedm zálohy. Každá záloha je přibližně 10 GB. Do sdílené složky byste měli mít k uložení 140 GB záloh. Další informace o výběru umístění úložiště pro službu Azure zásobníku infrastruktura zálohování najdete v tématu [zálohování řadiče požadavky](azure-stack-backup-reference.md#backup-controller-requirements).
- **Přihlašovací údaje**  
  Budete potřebovat účet uživatele domény a přihlašovací údaje, například můžete použít přihlašovací údaje správce Azure zásobníku.
- **Šifrovací klíč**  
  Záložní soubory jsou šifrované pomocí tohoto klíče. Ujistěte se, že tento klíč uložit na bezpečném místě. Jakmile poprvé nastavit tento klíč nebo klíč Otočit v budoucnu, nelze zobrazit, tento klíč z tohoto rozhraní. Další pokyny ke generování předsdílený klíč, postupujte podle skripty v [povolit zálohování pro zásobník Azure pomocí prostředí PowerShell](http://azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [povolit zálohování pro zásobník Azure z portálu pro správu](azure-stack-backup-enable-backup-console.md).
- Zjistěte, jak [povolení zálohování pro Azure zásobníku pomocí prostředí PowerShell](azure-stack-backup-enable-backup-powershell.md).
- Zjistěte, jak [zálohování Azure zásobníku](azure-stack-backup-back-up-azure-stack.md )
- Zjistěte, jak [zotavit závažné ztráty dat](azure-stack-backup-recover-data.md)

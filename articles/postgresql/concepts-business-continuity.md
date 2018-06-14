---
title: Přehled kontinuity podnikových procesů pomocí databáze Azure pro PostgreSQL
description: Přehled kontinuity podnikových procesů pomocí databáze Azure pro PostgreSQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 1b981b650d75556f4521aaf0f089443bb88d064a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29693407"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql"></a>Přehled kontinuity podnikových procesů pomocí databáze Azure pro PostgreSQL

Tento přehled popisuje možnosti, které poskytuje Azure databáze PostgreSQL pro provozní kontinuitu a zotavení po havárii. Informace o možnostech pro obnovení z rušivý událostí, které může dojít ke ztrátě dat nebo způsobit, že databáze a aplikace k dispozici. Zjistěte, co dělat, pokud uživatele nebo aplikace chyba ovlivňuje integritu dat, oblast Azure má výpadek nebo pokud vaše aplikace vyžaduje údržby.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funkce, které můžete použít k poskytování kontinuity podnikových procesů

Azure databázi PostgreSQL poskytuje funkce kontinuity firmy, které zahrnují automatizované zálohování a možnosti pro uživatele k zahájení geografické obnovení. Každý odhadovaná doba obnovení (Vložit) a potenciální ztrátě dat má jiné vlastnosti. Jakmile porozumíte tyto možnosti, můžete vybrat mezi nimi a používají se společně pro různé scénáře. Když budete vyvíjet plánu pro kontinuitu podnikových, musíte pochopit maximální přijatelnou dobu před aplikace plně obnoví po nepříjemným událostem – to je cíl doba obnovení (RTO). Také musíte porozumět maximální objem dat poslední aktualizace (časový interval) aplikace může tolerovat ztráty při obnovování po nepříjemným událostem – to je cíl bodu obnovení (RPO).

Následující tabulka porovnává vložit a plánovaný bod obnovení pro funkce k dispozici:

| **Funkce** | **Basic** | **Obecné účely** | **Optimalizované z hlediska paměti** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Obnovení k určitému bodu v čase ze zálohy | Všechny body obnovení v rámci dobu uchování | Všechny body obnovení v rámci dobu uchování | Všechny body obnovení v rámci dobu uchování |
| Geografické obnovení ze zálohy geograficky replikované | Nepodporuje se | Vložit < 12 h<br/>Plánovaný bod obnovení < 1 hod | Vložit < 12 h<br/>Plánovaný bod obnovení < 1 hod |

> [!IMPORTANT]
> Pokud server odstraníte, všechny databáze, které patří k serveru budou také odstraněny a nelze jej obnovit. Nelze obnovit odstraněné serveru.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Obnovení serveru po chybě uživatele nebo aplikace

Můžete obnovit server z různých rušivý událostí služby zálohování. Uživatel může omylem odstranit některá data, nechtěně vyřadit důležité tabulky nebo i vyřadit celé databáze. Aplikace může nechtěně dobrých dat přepsat chybná data z důvodu vadou aplikaci a tak dále.

Můžete provést bodu v čas – obnovení k vytvoření kopie vašeho serveru do známé dobré bodu v čase. Tento bod v čase musí být v období uchovávání záloh, které jste nakonfigurovali pro váš server. Po obnovení dat na nový server, můžete nahradit původní server nově obnovený serveru nebo zkopírovat požadovaná data ze serveru obnovené do původního serveru.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Obnovit z center výpadek služby Azure místní data

Přestože je taková situace výjimečná, i u datového centra Azure může dojít k výpadku. Pokud dojde k výpadku, budou obchodní přerušení, která může být pouze poslední několik minut, ale může trvat hodiny.

Jednou z možností je čekat na váš server vrátí do režimu online, když se data center výpadek nad. Tento postup funguje pro aplikace, které si může dovolit tak, aby měl server do offline režimu pro nějakou dobu, například vývojové prostředí. Pokud datového centra k výpadku, si nejste jisti, jak dlouho může poslední se výpadek, aby tato možnost funguje jenom v případě nepotřebujete váš server nějakou dobu.

Další možností je použít pro funkci na PostgreSQL geografické obnovení, která obnoví serveru pomocí geograficky redundantní zálohy databáze Azure. Tyto zálohy jsou dostupné i tehdy, když oblast, kterou server je hostován v režimu offline. Můžete obnovit z tyto zálohy do žádné jiné oblasti a aby server opět online.

> [!IMPORTANT]
> Geografické obnovení je možné, pokud jste zřídili server s geograficky redundantní úložiště záloh.

## <a name="next-steps"></a>Další postup
- Další informace o automatizované zálohování naleznete v tématu [záloh v Azure databáze PostgreSQL](concepts-backup.md). 
- Obnovit k určitému bodu v čase pomocí portálu Azure, najdete v tématu [obnovit databázi k určitému bodu v čase pomocí portálu Azure](howto-restore-server-portal.md).
- Obnovit k určitému bodu v čase pomocí rozhraní příkazového řádku Azure, najdete v tématu [obnovit databázi k určitému bodu v čase pomocí rozhraní příkazového řádku](howto-restore-server-cli.md).
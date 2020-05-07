---
title: Ochrana dat v Azure Stream Analytics
description: Tento článek vysvětluje, jak šifrovat soukromá data používaná Azure Stream Analytics úlohou.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 8d201beb2ff4aba815749b12a506d2292779cb82
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857282"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Ochrana dat v Azure Stream Analytics 

Azure Stream Analytics je plně spravovaná platforma jako služba, která umožňuje vytvářet kanály analýz v reálném čase. Všechny těžké zvedá, jako je zřizování clusterů, škálování uzlů, aby vyhovovaly vašemu využití a Správa vnitřních kontrolních bodů, se spravují na pozadí.

## <a name="encrypt-your-data"></a>Šifrování dat

Stream Analytics v rámci své infrastruktury automaticky využívaly osvědčené šifrovací standardy pro šifrování a zabezpečení vašich dat. Stream Analytics můžete jednoduše důvěřovat, aby bezpečně ukládala všechna vaše data, takže se nemusíte starat o správu infrastruktury.

Pokud chcete k šifrování vašich dat používat klíče spravované zákazníkem (CMK), můžete použít vlastní účet úložiště (pro obecné účely v1 nebo v2) k uložení libovolných privátních datových assetů vyžadovaných modulem runtime Stream Analytics. Váš účet úložiště může být podle potřeby zašifrovaný. Žádná z vašich privátních datových assetů se trvale neukládá pomocí infrastruktury Stream Analytics. 

Toto nastavení musí být nakonfigurované v době vytváření úlohy Stream Analytics a nedá se upravit v průběhu životního cyklu úlohy. Úpravy nebo odstranění úložiště používaného vaší Stream Analytics se nedoporučují. Pokud odstraníte účet úložiště, trvale odstraníte všechny soukromé datové prostředky, což způsobí selhání vaší úlohy. 

Aktualizace nebo otočení klíčů k účtu úložiště není možné pomocí Stream Analyticsového portálu. Klíče můžete aktualizovat pomocí rozhraní REST API.


## <a name="configure-storage-account-for-private-data"></a>Konfigurace účtu úložiště pro soukromá data 

Pomocí následujícího postupu můžete nakonfigurovat účet úložiště pro soukromé datové prostředky. Tato konfigurace se provádí z vaší Stream Analytics úlohy, nikoli z vašeho účtu úložiště.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**. 

1. V **Analytics** >seznamu výsledků vyberte **úlohu** Stream Analytics analýzy. 

1. Vyplňte stránku úlohy Stream Analytics s potřebnými podrobnostmi, jako je název, oblast a škálování. 

1. Zaškrtněte políčko, které uvádí *zabezpečení všech privátních datových assetů, které tato úloha potřebuje v účtu úložiště*.

1. Vyberte účet úložiště z vašeho předplatného. Všimněte si, že toto nastavení nelze upravovat v průběhu životního cyklu úlohy. 

   ![Nastavení účtu úložiště privátních dat](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Soukromé datové prostředky, které jsou uloženy

Všechna privátní data, která jsou potřeba k uchování pomocí Stream Analytics, se ukládají do svého účtu úložiště. Mezi soukromé datové prostředky patří: 

* Dotazy, které jste vytvořili, a jejich související konfigurace  

* Uživatelsky definované funkce 

* Kontrolní body vyžadované modulem runtime Stream Analytics

* Snímky referenčních dat 

Ukládají se také podrobnosti o připojení vašich prostředků, které používá vaše úloha Stream Analytics. Zašifrujte svůj účet úložiště, abyste zabezpečili všechna vaše data. 

Další informace o [nabídkách dodržování předpisů Microsoftu](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)vám pomůžou splnit vaše povinnosti dodržování předpisů v jakémkoli regulovaném odvětví nebo prostředí. 

## <a name="known-issues"></a>Známé problémy
K dispozici je známý problém, kdy úloha pomocí spravovaného klíče zákazníka spustí chyby při použití spravované identity k ověření pro jakékoli vstupy nebo výstupy. Oprava tohoto problému probíhá na a bude k dispozici v blízké budoucnosti. 

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md)
* [Vysvětlení vstupů pro Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Koncepty kontrolního bodu a přehrání v úlohách Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Použití referenčních dat pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md)

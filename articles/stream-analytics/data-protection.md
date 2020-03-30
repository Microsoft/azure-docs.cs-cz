---
title: Ochrana dat ve Službě Azure Stream Analytics
description: Tento článek vysvětluje, jak šifrovat vaše soukromá data používaná úlohou Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299392"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Ochrana dat ve Službě Azure Stream Analytics 

Azure Stream Analytics je plně spravovaná platforma jako služba, která vám umožní vytvářet kanály analýz v reálném čase. Všechny těžké zvedání, jako je například zřizování clusteru, škálování uzlů pro vaše použití a správa vnitřní kontrolní body, je spravovánna pozadí.

## <a name="encrypt-your-data"></a>Šifrování dat

Stream Analytics automaticky využívá nejlepší standardy šifrování ve své infrastruktuře k šifrování a zabezpečení vašich dat. Stream Analytics můžete jednoduše důvěřovat, že bude bezpečně ukládat všechna vaše data, abyste se nemuseli starat o správu infrastruktury.

Pokud chcete k šifrování dat použít klíče spravované zákazníkem (CMK), můžete použít svůj vlastní účet úložiště (univerzální v1 nebo V2) k ukládání všech privátních datových prostředků, které jsou vyžadovány za běhu Služby Stream Analytics. Váš účet úložiště lze podle potřeby zašifrovat. Žádná z vašich privátních datových prostředků není trvale uložena infrastrukturou Stream Analytics. 

Toto nastavení musí být nakonfigurováno v době vytváření úloh služby Stream Analytics a nelze ho v průběhu životního cyklu úlohy upravovat. Úprava nebo odstranění úložiště, které používá vaše Stream Analytics se nedoporučuje. Pokud svůj účet úložiště odstraníte, trvale odstraníte všechny prostředky soukromých dat, což způsobí selhání úlohy. 

Aktualizace nebo otočení klíčů k účtu úložiště není možné pomocí portálu Stream Analytics. Klíče můžete aktualizovat pomocí rest API.


## <a name="configure-storage-account-for-private-data"></a>Konfigurace účtu úložiště pro soukromá data 

Pomocí následujících kroků nakonfigurujte účet úložiště pro prostředky soukromých dat. Tato konfigurace se provádí z vaší úlohy Stream Analytics, ne z vašeho účtu úložiště.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**. 

1. V seznamu výsledků vyberte > **Úloha**  **Analytics**Stream Analytics. 

1. Vyplňte stránku úlohy Stream Analytics s nezbytnými podrobnostmi, jako je název, oblast a měřítko. 

1. Zaškrtněte políčko, které *uvádí: Zabezpečit všechny prostředky soukromých dat potřebné pro tuto úlohu v účtu úložiště*.

1. Vyberte účet úložiště z předplatného. Všimněte si, že toto nastavení nelze změnit v průběhu celého životního cyklu úlohy. 

   ![Nastavení účtu úložiště soukromých dat](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Prostředky soukromých dat, které jsou uloženy

Veškerá soukromá data, která musí Stream Analytics uchovávat, jsou uložena ve vašem účtu úložiště. Příklady prostředků soukromých dat: 

* Dotazy, které jste vytvořili, a související konfigurace  

* Uživatelsky definované funkce 

* Kontrolní body potřebné pro runtime Stream Analytics

* Snímky referenčních dat 

Podrobnosti o připojení vašich prostředků, které používá vaše úloha Stream Analytics, jsou také uloženy. Šifrujte svůj účet úložiště a zabezpečte všechna data. 

Chcete-li splnit povinnosti týkající se dodržování předpisů v jakémkoli regulovaném odvětví nebo prostředí, přečtěte si další informace o [nabídkách společnosti Microsoft v oblasti dodržování předpisů](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md)
* [Principy vstupů pro Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Koncepty kontrolního bodu a přehrávání v pracovních úlohách Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Použití referenčních dat pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md)

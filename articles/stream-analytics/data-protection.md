---
title: Ochrana dat v Azure Stream Analytics
description: Tento článek vysvětluje, jak šifrovat soukromá data používaná Azure Stream Analytics úlohou.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 76218003a40c02def3dc62ea8480022d9647938d
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723333"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Ochrana dat v Azure Stream Analytics 

Azure Stream Analytics je plně spravovaná platforma jako služba, která umožňuje vytvářet kanály analýz v reálném čase. Všechny těžké zvedá, jako je zřizování clusterů, škálování uzlů, aby vyhovovaly vašemu využití a Správa vnitřních kontrolních bodů, se spravují na pozadí.

## <a name="private-data-assets-that-are-stored"></a>Soukromé datové prostředky, které jsou uloženy

Azure Stream Analytics uchovává následující metadata a data, aby je bylo možné spustit: 

* Definice dotazu a jejich související konfigurace  

* Uživatelsky definované funkce nebo agregace  

* Kontrolní body vyžadované modulem runtime Stream Analytics

* Snímky referenčních dat 

* Podrobnosti o připojení prostředků používaných úlohou Stream Analytics

Další informace o [nabídkách dodržování předpisů Microsoftu](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)vám pomůžou splnit vaše povinnosti dodržování předpisů v jakémkoli regulovaném odvětví nebo prostředí. 

## <a name="in-region-data-residency"></a>In-Region zasídlí dat
Azure Stream Analytics ukládá zákaznická data a další výše popsané metadata. Zákaznická data se ve výchozím nastavení ukládají v rámci Azure Stream Analytics v jedné oblasti, takže tato služba automaticky splňuje požadavky na umístění dat v oblastech, včetně těch, které jsou uvedené v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/).
Kromě toho se můžete rozhodnout pro uložení všech datových assetů (zákaznických dat a dalších metadat) souvisejících s úlohou Stream Analytics v jedné oblasti tak, že je zašifrujete v účtu úložiště podle vašeho výběru.

## <a name="encrypt-your-data"></a>Šifrování dat

Stream Analytics v rámci své infrastruktury automaticky využívaly osvědčené šifrovací standardy pro šifrování a zabezpečení vašich dat. Stream Analytics můžete jednoduše důvěřovat, aby bezpečně ukládala všechna vaše data, takže se nemusíte starat o správu infrastruktury.

Pokud chcete použít klíče spravované zákazníkem k šifrování vašich dat, můžete použít vlastní účet úložiště (pro obecné účely v1 nebo v2) k uložení libovolných privátních datových assetů, které jsou vyžadovány modulem runtime Stream Analytics. Váš účet úložiště může být podle potřeby zašifrovaný. Žádná z vašich privátních datových assetů se trvale neukládá pomocí infrastruktury Stream Analytics. 

Toto nastavení musí být nakonfigurované v době vytváření úlohy Stream Analytics a nedá se upravit v průběhu životního cyklu úlohy. Úpravy nebo odstranění úložiště používaného vaší Stream Analytics se nedoporučují. Pokud odstraníte účet úložiště, trvale odstraníte všechny soukromé datové prostředky, což způsobí selhání vaší úlohy. 

Aktualizace nebo otočení klíčů k účtu úložiště není možné pomocí Stream Analyticsového portálu. Klíče můžete aktualizovat pomocí rozhraní REST API.


### <a name="configure-storage-account-for-private-data"></a>Konfigurace účtu úložiště pro soukromá data 

Zašifrujte svůj účet úložiště, abyste zabezpečili všechna vaše data, a explicitně zvolíte umístění vašich privátních dat. 

Další informace o [nabídkách dodržování předpisů Microsoftu](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)vám pomůžou splnit vaše povinnosti dodržování předpisů v jakémkoli regulovaném odvětví nebo prostředí. 

Pomocí následujícího postupu můžete nakonfigurovat účet úložiště pro soukromé datové prostředky. Tato konfigurace se provádí z vaší Stream Analytics úlohy, nikoli z vašeho účtu úložiště.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**. 

1.  ****   > V seznamu výsledků vyberte **úlohu Stream Analytics** analýzy   . 

1. Vyplňte stránku úlohy Stream Analytics s potřebnými podrobnostmi, jako je název, oblast a škálování. 

1. Zaškrtněte políčko, které uvádí *zabezpečení všech privátních datových assetů, které tato úloha potřebuje v účtu úložiště*.

1. Vyberte účet úložiště z vašeho předplatného. Všimněte si, že toto nastavení nelze upravovat v průběhu životního cyklu úlohy. Tuto možnost po vytvoření úlohy také nemůžete přidat.

1. Chcete-li provést ověření pomocí připojovacího řetězce, v rozevíracím seznamu režim ověřování vyberte možnost **připojovací řetězec** . Klíč účtu úložiště se automaticky vyplní z vašeho předplatného.

   ![Nastavení účtu úložiště privátních dat](./media/data-protection/storage-account-create.png)

1. Pokud se chcete ověřit pomocí spravované identity (Preview), vyberte v rozevíracím seznamu režim ověřování možnost **spravovaná identita** . Pokud zvolíte spravovaná identita, budete muset přidat svou Stream Analyticsovou úlohu do seznamu řízení přístupu účtu úložiště s rolí *Přispěvatel dat objektu BLOB úložiště* . Pokud neudělíte přístup k úloze, úloha nebude moci provádět žádné operace. Další informace o tom, jak udělit přístup, najdete v tématu [použití služby Azure RBAC k přiřazení spravované identity k jinému prostředku](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource).

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="Nastavení účtu úložiště privátních dat s ověřováním spravovaných identit":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>Soukromé datové prostředky, které jsou uloženy pomocí Stream Analytics

Všechna privátní data, která jsou potřeba k uchování pomocí Stream Analytics, se ukládají do svého účtu úložiště. Mezi soukromé datové prostředky patří: 

* Dotazy, které jste vytvořili, a jejich související konfigurace  

* Uživatelsky definované funkce 

* Kontrolní body vyžadované modulem runtime Stream Analytics

* Snímky referenčních dat 

Ukládají se také podrobnosti o připojení vašich prostředků, které používá vaše úloha Stream Analytics. Zašifrujte svůj účet úložiště, abyste zabezpečili všechna vaše data. 

Další informace o [nabídkách dodržování předpisů Microsoftu](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)vám pomůžou splnit vaše povinnosti dodržování předpisů v jakémkoli regulovaném odvětví nebo prostředí. 

## <a name="enables-data-residency"></a>Povoluje zasídlí dat 
Tuto funkci můžete použít k vykonání požadavků na všechny požadavky na data, které můžete mít v souladu s odpovídajícím účtem úložiště.

## <a name="known-issues"></a>Známé problémy
K dispozici je známý problém, kdy úloha pomocí spravovaného klíče zákazníka spustí chyby při použití spravované identity k ověření pro jakékoli vstupy nebo výstupy. 

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md)
* [Vysvětlení vstupů pro Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Koncepty kontrolního bodu a přehrání v úlohách Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Použití referenčních dat pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md)

---
title: Prevence náhodného odstranění – sdílené složky Azure
description: Přečtěte si o obnovitelném odstranění sdílených složek Azure a o tom, jak ho můžete použít k obnovení dat a zabránění nechtěnému odstranění.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: a879aff7c3fcaa7ac2c15be295c6c5bdca25ccdf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937290"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Zabránit náhodnému odstranění sdílených složek Azure

Azure Storage teď nabízí obnovitelné odstranění pro sdílené složky. Obnovitelné odstranění umožňuje obnovit sdílenou složku, když ji omylem odstraníte pomocí aplikace nebo jiného uživatele účtu úložiště.

## <a name="how-soft-delete-works"></a>Jak funguje obnovitelné odstranění

Pokud je pro sdílené složky Azure povolené obnovitelné odstranění, pokud je sdílená složka Odstraněná, přepne se do stavu bez trvalého vymazání. Můžete nakonfigurovat dobu, po kterou se Odstraněná data odstraněná mají obnovit, než se trvale odstraní, a během této doby uchování zrušit její sdílení kdykoli. Po obnovení bude sdílená složka a veškerý obsah včetně snímků obnovena do stavu před odstraněním. Obnovitelné odstranění funguje jenom na úrovni sdílené složky – jednotlivé soubory, které se odstranily, se pořád vymažou trvale.

Obnovitelné odstranění lze povolit buď pro nové, nebo existující sdílené složky. Obnovitelné odstranění je také zpětně kompatibilní, takže nemusíte dělat žádné změny v aplikacích, abyste mohli využít výhod ochrany obnovitelného odstranění. 

Pokud chcete trvale odstranit sdílenou složku ve stavu tichého odstranění před jeho vypršení platnosti, musíte zrušit jeho odstranění, zakázat obnovitelné odstranění a pak znovu odstranit sdílenou složku. Pak byste měli obnovitelné odstranění znovu povolit, protože jakékoli jiné sdílené složky v tomto účtu úložiště budou kvůli nechtěnému odstranění zranitelné.

U dočasně odstraněných souborů Premium se v celkovém výpočtu kvóty účtu úložiště používá kvóta sdílené složky (zřízená velikost sdílené složky) až do data vypršení platnosti nedokončené sdílené složky, když se sdílená složka zcela odstraní.

## <a name="configuration-settings"></a>Nastavení konfigurace

### <a name="enabling-or-disabling-soft-delete"></a>Povolení nebo zakázání obnovitelného odstranění

Obnovitelné odstranění sdílených složek je na úrovni účtu úložiště povolené. v důsledku toho se nastavení obnovitelného odstranění aplikuje na všechny sdílené složky v rámci účtu úložiště. Obnovitelné odstranění je ve výchozím nastavení povoleno pro nové účty úložiště a lze je kdykoli zakázat nebo povolit. Obnovitelné odstranění není automaticky povolené pro existující účty úložiště, pokud se pro sdílenou složku Azure v tomto účtu úložiště nenakonfigurovalo [zálohování sdílené složky Azure](../../backup/azure-file-share-backup-overview.md) . Pokud je zálohování sdílené složky Azure nakonfigurované, pak se v účtu úložiště této sdílené složky automaticky povolí obnovitelné odstranění pro sdílené složky Azure.

Pokud pro sdílené složky povolíte obnovitelné odstranění, odstraňte některé sdílené složky a pak zakažte možnost obnovitelné odstranění. Pokud se sdílené složky uložily v tomto období, můžete ke sdíleným složkám pořád přistupovat a obnovovat je. Pokud povolíte obnovitelné odstranění, musíte také nakonfigurovat dobu uchovávání.

### <a name="retention-period"></a>Doba uchovávání

Doba uchování je doba, po kterou jsou dočasné odstraněné sdílené složky uložené a dostupné pro obnovení. U sdílených složek, které jsou explicitně odstraněny, se po odstranění dat spustí hodiny doby uchování. V současné době můžete zadat dobu uchování mezi 1 a 365 dny. Dobu uchování obnovitelného odstranění můžete kdykoli změnit. Aktualizovaná doba uchovávání se bude vztahovat jenom na sdílené složky, které se odstranily po aktualizaci doby uchování. Sdílené složky, které se odstranily před aktualizací doby uchování, vyprší na základě doby uchování, která byla nakonfigurovaná při odstranění těchto dat.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Pro sdílené složky Standard i Premium se místo zřízené kapacity účtují využitá kapacita při neúplném odstranění. Kromě toho se za sdílené složky Premium účtují podle snímkové rychlosti ve stavu obnovitelného odstranění. Standardní sdílené složky se účtují v pravidelných intervalech ve stavu tichého odstranění. Za data, která se po nakonfigurované době uchování trvale odstraní, se vám neúčtují žádné poplatky.

Další informace o cenách pro Azure File Storage obecně najdete na [stránce s cenami za azure File Storage](https://azure.microsoft.com/pricing/details/storage/files/).

Když na začátku povolíte obnovitelné odstranění, doporučujeme vám používat malou dobu uchovávání, abyste lépe pochopili, jak tato funkce ovlivní vaše vyúčtování.

## <a name="next-steps"></a>Další kroky

Chcete-li zjistit, jak povolit a používat obnovitelné odstranění, pokračujte v [Povolení obnovitelného odstranění](storage-files-enable-soft-delete.md).

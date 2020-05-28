---
title: Prevence náhodného odstranění – sdílené složky Azure
description: Přečtěte si o obnovitelném odstranění sdílených složek Azure a o tom, jak ho můžete použít k obnovení dat a zabránění nechtěnému odstranění.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 9ffc065cb877c7f87cd38671f586f0754a42b2b8
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141581"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Zabránit náhodnému odstranění sdílených složek Azure

Azure Storage teď nabízí obnovitelné odstranění pro sdílené složky (Preview). Obnovitelné odstranění umožňuje obnovit data v případě, že je omylem odstraněno aplikací nebo jiným uživatelem účtu úložiště.

## <a name="how-soft-delete-preview-works"></a>Jak funguje obnovitelné odstranění (Preview)

Když se tato možnost povolí, obnovitelné odstranění vám umožní ukládat a obnovovat sdílené složky při jejich odstranění. Při odstranění dat se místo trvalého vymazání přepne na stav neodstraněno. Můžete nakonfigurovat dobu, po kterou se Odstraněná data budou moct obnovit, než se trvale odstraní.

Obnovitelné odstranění lze povolit pro nové nebo existující sdílené složky. Obnovitelné odstranění je také zpětně kompatibilní, takže nemusíte dělat žádné změny v aplikacích, abyste mohli využít výhod ochrany obnovitelného odstranění. 

U dočasně odstraněných souborů Premium se v celkovém výpočtu kvóty účtu úložiště používá kvóta sdílené složky (zřízená velikost sdílené složky) až do data vypršení platnosti nedokončené sdílené složky, když se sdílená složka zcela odstraní.

### <a name="availability"></a>Dostupnost

Obnovitelné odstranění pro sdílené složky Azure (Preview) je dostupné na všech úrovních úložiště, všech typech účtů úložiště a v každé oblasti, ve které jsou soubory Azure dostupné.

## <a name="configuration-settings"></a>Nastavení konfigurace

Obnovitelné odstranění sdílených složek je povolené na úrovni účtu úložiště. nastavení obnovitelného odstranění se aplikuje na všechny sdílené složky v rámci účtu úložiště. Při vytváření nového účtu úložiště je obnovitelné odstranění ve výchozím nastavení vypnuté. U stávajících účtů úložiště je ve výchozím nastavení vypnuté i obnovitelné odstranění. Obnovitelné odstranění můžete kdykoli zapnout nebo vypnout.

Pokud pro sdílené složky povolíte obnovitelné odstranění, odstraňte některé sdílené složky a pak zakažte možnost obnovitelné odstranění. Pokud se sdílené složky uložily v tomto období, můžete ke sdíleným složkám pořád přistupovat a obnovovat je. Pokud povolíte obnovitelné odstranění, musíte také nakonfigurovat dobu uchovávání.

Doba uchování označuje dobu, po kterou jsou dočasné odstraněné sdílené složky uložené a dostupné pro obnovení. U sdílených složek, které jsou explicitně odstraněny, se po odstranění dat spustí hodiny doby uchování. V současné době je možné nechat odstraněné sdílené složky po dobu 1 až 365 dnů.

Dobu uchování obnovitelného odstranění můžete kdykoli změnit. Aktualizovaná doba uchovávání se bude vztahovat jenom na sdílené složky, které se odstranily po aktualizaci doby uchování. Sdílené složky, které se odstranily před aktualizací doby uchování, vyprší na základě doby uchování, která byla nakonfigurovaná při odstranění těchto dat.

Chcete-li trvale odstranit sdílenou složku ve stavu tichého odstranění před uplynutím doby jejího vypršení platnosti, je nutné tuto sdílenou složku obnovit, zakázat obnovitelné odstranění a pak znovu odstranit sdílenou složku. Pak byste měli znovu povolit obnovitelné odstranění, protože jakékoli jiné sdílené složky v tomto účtu úložiště budou kvůli nechtěnému odstranění zranitelné.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Pro sdílené složky Standard i Premium se místo zřízené kapacity účtují využitá kapacita při neúplném odstranění. Kromě toho se za sdílené složky Premium účtují podle snímkové rychlosti ve stavu obnovitelného odstranění. Standardní sdílené složky se účtují v pravidelných intervalech ve stavu tichého odstranění. Za data, která se po nakonfigurované době uchování trvale odstraní, se vám neúčtují žádné poplatky.

Další informace o cenách pro Azure File Storage obecně najdete na [stránce s cenami za azure File Storage](https://azure.microsoft.com/pricing/details/storage/files/).

Když na začátku povolíte obnovitelné odstranění, doporučujeme vám používat malou dobu uchovávání, abyste lépe pochopili, jak tato funkce ovlivní vaše vyúčtování.

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jak povolit a používat obnovitelné odstranění, pokračujte v [Povolení obnovitelného odstranění](storage-files-enable-soft-delete.md) .

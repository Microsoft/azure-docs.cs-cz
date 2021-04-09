---
title: Řešení potíží se službou Azure Data Share
description: Naučte se řešit problémy s pozvánkami a chybami při vytváření nebo přijímání sdílených dat v Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.openlocfilehash: 3aa1c0b8579bd37d2bb51cbde70997131c696813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964503"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Řešení běžných problémů v Azure Data Share 

Tento článek vysvětluje, jak řešit běžné problémy v Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Pozvánky ke sdílení dat Azure 

V některých případech se může stát, že když nový uživatel vybere možnost **přijmout pozvánku** v e-mailové pozvánce, uvidí prázdný seznam pozvánek. 

:::image type="content" source="media/no-invites.png" alt-text="Snímek obrazovky znázorňující prázdný seznam pozvánek":::

Tento problém může mít jednu z následujících příčin:

* **Služba Azure Data Share není v tenantovi Azure registrována jako poskytovatel prostředku žádného předplatného Azure.** K tomuto problému dochází, když váš tenant Azure nemá žádný prostředek pro sdílení dat. 

    Když vytvoříte prostředek služby Azure Data Share, ve vašem předplatném se automaticky zaregistruje poskytovatel prostředků. Službu sdílení dat můžete ručně zaregistrovat pomocí následujících kroků. K provedení těchto kroků potřebujete [roli přispěvatele](../role-based-access-control/built-in-roles.md#contributor) pro předplatné Azure. 

    1. Na webu Azure Portal přejděte na **Předplatná**.
    1. Vyberte předplatné, které chcete použít k vytvoření prostředku Azure Data Share.
    1. Vyberte **poskytovatelé prostředků**.
    1. Vyhledejte **Microsoft. datashare**.
    1. Vyberte **Zaregistrovat**.

* **Pozvánka se pošle na váš e-mailový alias místo na přihlašovací e-mailovou adresu Azure.** Pokud jste už zaregistrovali službu Azure Data Share Service nebo jste vytvořili prostředek pro sdílení dat v tenantovi Azure, ale pořád nevidíte pozvánku, váš e-mailový alias může být uvedený jako příjemce. Kontaktujte poskytovatele dat a zajistěte, aby se Pozvánka poslala e-mailovou adresou pro Azure, a ne vaším e-mailovým aliasem.

* **Pozvánka již byla přijata.** Odkaz v e-mailu vás přesměruje na stránku **pozvánky ke sdílení dat** v Azure Portal. Tato stránka obsahuje jenom pozvánky, které čekají na vyřízení. Přijaté pozvánky se nezobrazí na stránce. Pokud chcete zobrazit přijaté sdílené složky a nakonfigurovat cílové nastavení clusteru Azure Průzkumník dat, přečtěte si prostředek pro sdílení dat, který jste použili k přijetí pozvánky.

## <a name="creating-and-receiving-shares"></a>Vytváření a přijímání sdílených složek

Při vytváření nové sdílené složky, přidání datových sad nebo mapování datových sad se můžou zobrazit následující chyby:

* Nepovedlo se přidat datové sady.
* Nepovedlo se namapovat datové sady.
* Nelze udělit prostředku sdílení dat x přístup k jednotce y.
* Nemáte správná oprávnění k x.
* Nemohli jsme přidat oprávnění k zápisu pro účet Azure Data Share k jednomu nebo více vybraným prostředkům.

Pokud nemáte dostatečná oprávnění k úložišti dat Azure, může se zobrazit jedna z těchto chyb. Další informace najdete části [Role a požadavky](concepts-roles-permissions.md). 

Ke sdílení nebo příjmu dat z úložiště dat Azure potřebujete oprávnění k zápisu. Toto oprávnění je obvykle součástí role přispěvatele. 

Pokud sdílíte data nebo odesíláte data z úložiště dat Azure poprvé, potřebujete také oprávnění *Microsoft. Authorization/role/zapisovat* . Toto oprávnění je obvykle součástí role vlastníka. I když jste vytvořili prostředek úložiště dat Azure, nebudete nutně nutně vlastníkem tohoto prostředku. 

Pokud máte správná oprávnění, služba Azure Data Share automaticky povolí spravované identitě prostředku sdílet data pro přístup k úložišti dat. Tento proces může trvat několik minut. Pokud při této prodlevě dojde k chybě, zkuste to znovu za několik minut.

Sdílení založené na SQL vyžaduje další oprávnění. Informace o požadavcích najdete v tématu [sdílení ze zdrojů SQL](how-to-share-from-sql.md).

## <a name="snapshots"></a>Snímky
Snímek může z různých důvodů selhat. Otevřete podrobnou chybovou zprávu výběrem počátečního času snímku a stavu každé datové sady. 

Snímky se běžně nedaří z těchto důvodů:

* Sdílení dat nemá oprávnění ke čtení ze zdrojového úložiště dat nebo k zápisu do cílového úložiště dat. Další informace najdete části [Role a požadavky](concepts-roles-permissions.md). Pokud pořídíte snímek poprvé, prostředek sdílení dat může potřebovat několik minut, než bude moct získat přístup k úložišti dat Azure. Po několika minutách to zkuste znovu.
* Připojení ke zdrojovému úložišti dat nebo cílovému úložišti dat je blokováno bránou firewall.
* Byla odstraněna sdílená datová sada, zdrojové úložiště dat nebo cílové úložiště dat.

U účtů úložiště může snímek selhat, protože se ve zdrojovém souboru aktualizuje soubor, zatímco se snímek děje. V důsledku toho se může v cíli zobrazit soubor o velikosti 0 bajtů. Po aktualizaci ve zdroji by měly být snímky úspěšné.

U zdrojů SQL může být snímek neúspěšný z následujících důvodů:

* Zdrojový skript SQL nebo cílový skript SQL, který uděluje oprávnění ke sdílení dat, neběží. Nebo pro Azure SQL Database nebo Azure synapse Analytics (dřív Azure SQL Data Warehouse) se skript spouští pomocí ověřování SQL místo ověřování Azure Active Directory.  
* Zdrojové úložiště dat nebo cílové úložiště dat SQL je pozastaveno.
* Proces snímku nebo cílové úložiště dat nepodporuje datové typy SQL. Další informace najdete v tématu [sdílení ze zdrojů SQL](how-to-share-from-sql.md#supported-data-types).
* Zdrojové úložiště dat nebo cílové úložiště dat SQL jsou zamčené jinými procesy. Sdílení dat Azure tyto datové úložiště nezamkne. Stávající zámky v těchto úložištích dat ale můžou vytvořit snímek neúspěšný.
* Cílová tabulka SQL je odkazována omezením cizího klíče. Pokud má cílová tabulka v průběhu snímku stejný název jako tabulka ve zdrojových datech, Azure Data Share tuto tabulku ponechá a vytvoří novou tabulku. Pokud je cílová tabulka SQL odkazována omezením cizího klíče, nelze tabulku vyřadit.
* Je vygenerován cílový soubor CSV, ale data nelze číst v aplikaci Excel. Tento problém může být zobrazen, pokud zdrojová tabulka SQL obsahuje data, která obsahují jiné než anglické znaky. V Excelu vyberte kartu **získat data** a zvolte soubor CSV. Vyberte položku původ souboru **65001: Unicode (UTF-8)** a potom data načtěte.

## <a name="updated-snapshot-schedules"></a>Aktualizované plány snímků
Poté, co poskytovatel dat aktualizuje plán snímku pro odeslanou sdílenou složku, musí datový příjemce zakázat předchozí plán snímku. Pak povolte aktualizovaný plán snímků pro přijatou sdílenou složku. 

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte v kurzu [sdílení dat](share-your-data.md) . 

Pokud se chcete dozvědět, jak přijímat data, přejděte k kurzu [přijetí a přijetí dat](subscribe-to-data-share.md) .
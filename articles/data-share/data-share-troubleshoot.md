---
title: Řešení potíží se službou Azure Data Share
description: Naučte se řešit problémy s požadavky a chybami při vytváření nebo přijímání sdílených dat pomocí Azure Data Share.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 10/02/2020
ms.openlocfilehash: 620fe1e693a177123e166220ab94bbd74c4826ff
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761527"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Řešení běžných potíží se službou Azure Data Share 

Tento článek popisuje, jak řešit běžné problémy s Azure Data Share. 

## <a name="azure-data-share-invitations"></a>Pozvánky ke sdílení dat Azure 

V některých případech platí, že když nový uživatel klikne na **přijmout pozvánku** z e-mailové pozvánky, která se poslala, může se zobrazit prázdný seznam pozvánek. 

![Žádná pozvání](media/no-invites.png)

Může k tomu dojít z následujících důvodů:

* **Služba Azure Data Share není zaregistrovaná jako poskytovatel prostředků žádného předplatného Azure v daném tenantovi Azure.** K tomuto problému dojde v případě, že ve vašem tenantovi Azure není žádný prostředek služby Data Share. Když vytvoříte prostředek služby Azure Data Share, ve vašem předplatném se automaticky zaregistruje poskytovatel prostředků. Službu Data Share můžete zaregistrovat také ručně pomocí následujících kroků. K dokončení těchto kroků budete potřebovat roli přispěvatele Azure.

    1. Na webu Azure Portal přejděte do části **Předplatná**.
    1. Vyberte předplatné, které chcete použít k vytvoření prostředku služby Azure Data Share.
    1. Klikněte na **Poskytovatelé prostředků**.
    1. Vyhledejte **Microsoft.DataShare**.
    1. Klikněte na **Zaregistrovat**. 

    Abyste mohli dokončit tento postup, musíte mít [roli přispěvatele Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) v rámci předplatného Azure. 

* **Pozvánka se neodeslala na váš přihlašovací e-mail pro Azure, ale na váš e-mailový alias.** Pokud jste již zaregistrovali službu Azure Data Share nebo jste v tenantovi Azure již vytvořili prostředek služby Data Share, ale pozvánka se vám stále nezobrazuje, důvodem může být to, že poskytovatel jako příjemce zadal váš e-mailový alias, a ne vaši přihlašovací e-mailovou adresu pro Azure. Obraťte se na poskytovatele dat a ujistěte se, že odeslal pozvánku na vaši přihlašovací e-mailovou adresu pro Azure, a ne na váš e-mailový alias.

* **Pozvánka se již přijala.** Odkaz v e-mailu vás přesměruje na stránku pozvánek do služby Data Share na webu Azure Portal, na které se zobrazí pouze nevyřízené pozvánky. Pokud jste pozvánku již přijali, na stránce pozvánek do služby Data Share se již nezobrazí. Pokud chcete zobrazit přijaté sdílené složky a nakonfigurovat nastavení cílového clusteru Azure Data Exploreru, přejděte k prostředku služby Data Share, ve kterém jste přijali pozvánku.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Chyba při vytváření nebo přijímání nové sdílené složky

"Nepovedlo se přidat datové sady."

Nepovedlo se namapovat datové sady.

"Nepovedlo se udělit prostředku sdílení dat x přístup k y"

"Nemáte správná oprávnění k x"

"Nemohli jsme přidat oprávnění k zápisu pro účet Azure Data Share k jednomu nebo více vybraným prostředkům"

Pokud při vytváření nové sdílené složky nebo mapování datových sad dojde k některé z výše uvedených chyb, může to být způsobeno nedostatečnými oprávněními pro úložiště dat Azure. Další informace najdete v tématu [role a požadavky](concepts-roles-permissions.md) pro požadovaná oprávnění. 

Ke sdílení nebo příjmu dat z úložiště dat Azure, které obvykle existuje v roli přispěvatel, potřebujete oprávnění k zápisu. 

Pokud se jedná o první sdílení nebo příjem dat z úložiště dat Azure, potřebujete také *Microsoft. Authorization/role nebo* oprávnění k zápisu, které obvykle existují v roli vlastníka. I když jste vytvořili prostředek Azure Data Store, nevytvoří automaticky vlastníka tohoto prostředku. Pomocí správného oprávnění služba Azure Data Share automaticky uděluje přístup spravované identitě prostředku ke sdílení dat do úložiště dat. Tento proces může trvat několik minut, než se projeví. Pokud dojde k selhání kvůli této prodlevě, zkuste to znovu za několik minut.

Sdílení založené na SQL vyžaduje další oprávnění. Podrobný seznam požadovaných součástí najdete v tématu [sdílení ze zdrojů SQL](how-to-share-from-sql.md) .

## <a name="snapshot-failed"></a>Nepodařilo se vytvořit snímek
Snímek se kvůli nejrůznějším důvodům nezdařil. Podrobnou chybovou zprávu najdete tak, že kliknete na počáteční čas snímku a pak na stav každé datové sady. Níže jsou uvedené důvody, proč se snímek nezdařil:

* Sdílení dat nemá oprávnění ke čtení ze zdrojového úložiště dat nebo k zápisu do cílového úložiště dat. Podrobné požadavky na oprávnění najdete v tématu [role a požadavky](concepts-roles-permissions.md) . Pokud snímek vyberete poprvé, může trvat několik minut, než se prostředku sdílení dat udělí přístup k úložišti dat Azure. Počkejte pár minut a zkuste to znovu.
* Firewall zablokuje připojení ke zdroji dat nebo cílovému úložišti dat.
* Sdílená datová sada nebo zdrojové nebo cílové úložiště dat se odstraní.
* V případě sdílení SQL nejsou datové typy podporovány procesem snímku nebo cílovým úložištěm dat. Podrobnosti najdete [v tématu sdílení ze zdrojů SQL](how-to-share-from-sql.md#supported-data-types) .

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) . 

Pokud se chcete dozvědět, jak přijímat data, přejděte k kurzu [přijetí a přijetí dat](subscribe-to-data-share.md) .


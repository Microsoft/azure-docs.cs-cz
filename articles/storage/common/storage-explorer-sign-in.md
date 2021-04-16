---
title: Přihlaste se k Průzkumník služby Azure Storage | Microsoft Docs
description: Dokumentace k přihlašování do Průzkumník služby Azure Storage
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: chuye
ms.openlocfilehash: ef02842d189746a1801d97f91b92f249947c832d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568684"
---
# <a name="sign-in-to-storage-explorer"></a>Přihlášení k Průzkumník služby Storage

Přihlášení je doporučený způsob, jak získat přístup k prostředkům Azure Storage pomocí Průzkumník služby Storage. Když se přihlásíte, můžete využít oprávnění zálohovaných pomocí Azure AD, například řízení přístupu RBAC a Gen2 POSIX. 

## <a name="how-to-sign-in"></a>Jak se přihlásit

Pokud se chcete přihlásit k Průzkumník služby Storage, otevřete **dialogové okno připojit**. **Dialog připojit** můžete otevřít buď pomocí levého svislého panelu nástrojů, nebo kliknutím na **Přidat účet...** na **panelu účet**.

Po otevření dialogového okna vyberte **předplatné** jako typ prostředku, ke kterému se chcete připojit, a klikněte na **Další**.

Teď musíte vybrat prostředí Azure, ke kterému se chcete přihlásit. Můžete si vybrat ze všech známých prostředí, jako je Azure nebo Azure Čína, nebo můžete přidat vlastní prostředí. Po výběru prostředí klikněte na **Další**.

V tomto okamžiku se spustí **výchozí webový prohlížeč** s operačním systémem a otevře se přihlašovací stránka. Pro dosažení nejlepších výsledků nechte toto okno prohlížeče otevřené, pokud používáte Průzkumník služby Storage nebo alespoň, dokud neprovedete všechny očekávané MFA. Po dokončení přihlášení se můžete vrátit na Průzkumník služby Storage.

## <a name="managing-accounts"></a>Správa účtů

Můžete spravovat a odebírat účty Azure, ke kterým jste se přihlásili z **panelu účet**. **Panel účtu** můžete otevřít kliknutím na tlačítko **Spravovat účty** na panelu nástrojů vlevo na levé straně.

Na **panelu účet** se zobrazí všechny účty, ke kterým jste se přihlásili. V rámci každého účtu bude:
- Klienti, ke kterým účet patří
- Pro každého tenanta, předplatná, ke kterým máte přístup

Ve výchozím nastavení Průzkumník služby Storage pouze vás přihlásí k vašemu domovskému klientovi. Pokud chcete zobrazit předplatná a prostředky z jiného tenanta, musíte tohoto tenanta aktivovat. Chcete-li aktivovat tenanta, zaškrtněte políčko vedle něj. Po dokončení práce s klientem můžete zrušit zaškrtnutí jeho políčka a deaktivovat ho. Nemůžete deaktivovat svého domovského tenanta.

Po aktivaci tenanta možná budete muset znovu zadat svoje přihlašovací údaje, abyste mohli Průzkumník služby Storage načíst předplatná nebo získat přístup k prostředkům z tenanta. Opětovné zadání přihlašovacích údajů se obvykle stává kvůli zásadám podmíněného přístupu (CA), jako je vícefaktorové ověřování (MFA). A i když jste už možná provedli MFA pro jiného tenanta, možná to budete muset udělat znovu. Pokud chcete znovu zadat svoje přihlašovací údaje, stačí kliknout na **znovu zadat přihlašovací údaje...**. Můžete také kliknout na **Podrobnosti o chybě...** , abyste viděli přesně, proč se odběry nepodařilo načíst.

Po načtení předplatných můžete zvolit, která z nich chcete filtrovat/vyfiltrovat zaškrtnutím nebo zrušením zaškrtnutí jejich zaškrtávacích políček.

Pokud chcete odebrat celý účet Azure, klikněte na **Odebrat** vedle účtu.

## <a name="changing-where-sign-in-happens"></a>Změna místa, kde se přihlášení stane

Ve výchozím nastavení se k tomu přihlašuje ve **výchozím webovém prohlížeči** vašeho operačního systému. Přihlášení pomocí výchozího webového prohlížeče zjednodušuje přístup k prostředkům zabezpečeným pomocí zásad certifikační autority, jako je MFA. Pokud z nějakého důvodu nefunguje přihlášení pomocí **výchozího webového prohlížeče** s operačním systémem, můžete změnit, kde nebo jak Průzkumník služby Storage provádí přihlášení.

V části **Nastavení**  >    >  **přihlášení** aplikace vyhledejte nastavení **Přihlásit se** . Existují tři možnosti:
- **Výchozí webový prohlížeč**: přihlášení se zobrazí ve **výchozím webovém prohlížeči** vašeho operačního systému. Tato možnost se doporučuje.
- **Integrované přihlašování**: k přihlášení dojde v Průzkumník služby Storagem okně. Tato možnost může být užitečná, pokud se pokoušíte přihlásit pomocí několika účtů Microsoft (účty spravované služby) najednou. Pokud zvolíte tuto možnost, může dojít k problémům s některými zásadami certifikační autority.
- **Tok kódu zařízení**: Průzkumník služby Storage vám poskytne kód pro přechod do okna prohlížeče. Tato možnost se nedoporučuje. Tok kódu zařízení není kompatibilní s mnoha zásadami certifikační autority.

## <a name="troubleshooting-sign-in-issues"></a>Řešení potíží s přihlášením

Pokud máte potíže s přihlášením nebo máte po přihlášení k účtu Azure nějaké problémy, přečtěte si [část přihlášení v Průvodci odstraňováním potíží Průzkumník služby Storage](./storage-explorer-troubleshooting.md#sign-in-issues).

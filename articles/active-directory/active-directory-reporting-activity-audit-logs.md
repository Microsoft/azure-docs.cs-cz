---
title: Sestavy aktivit auditu na portálu Azure Active Directory | Dokumentace Microsoftu
description: Seznámení se sestavami aktivit auditu na portálu Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 04/19/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2565f31bdac59c3fbbb1d9d9a516b7bbf31d4d4c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222098"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit auditu na portálu Azure Active Directory 

Generování sestav v Azure Active Directory (Azure AD) umožňuje získat všechny informace potřebné ke zjištění stavu vašeho prostředí.

Architektura generování sestav v Azure AD se skládá z následujících komponent:

- **Aktivita** 
    - **Aktivity přihlašování** – informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů
    - **Protokoly auditu** – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou uživatelé, aplikace, skupiny, role, zásady, ověřování atd.
- **Zabezpečení** 
    - **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. Další podrobnosti najdete v tématu Riziková přihlášení.
    - **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. Další podrobnosti najdete v tématu Uživatelé označení příznakem rizika.

V toto tématu najdete přehled aktivit auditu.
 
## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?
* Uživatelé v roli Správce zabezpečení nebo Čtenář zabezpečení
* Globální správci
* Jednotliví uživatelé (bez oprávnění správce) mohou zobrazit své vlastní aktivity.


## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu v Azure Active Directory obsahují záznamy aktivit systému pro zajištění dodržování předpisů.  
Prvním vstupním bodem k veškerým datům auditování je možnost **Protokoly auditu** v oddílu **Aktivita** služby **Azure Active Directory**.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/61.png "Protokoly auditu")

Protokol auditu má výchozí zobrazení seznamu, které obsahuje následující položky:

- datum a čas výskytu
- iniciátor/aktér aktivity (*kdo*) 
- aktivita (*co*) 
- cíl

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/18.png "Protokoly auditu")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/19.png "Protokoly auditu")

To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/21.png "Protokoly auditu")


Kliknutím na položku v zobrazení seznamu k ní zobrazíte všechny dostupné podrobnosti.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/22.png "Protokoly auditu")


## <a name="filtering-audit-logs"></a>Filtrování protokolů auditu

Abyste omezili zobrazovaná data na úroveň, která vám vyhovuje, můžete filtrovat data přihlašování s využitím následujících polí:

- Rozsah dat
- Spustil(a) (činitel)
- Kategorie
- Typ prostředku aktivity
- Aktivita

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/23.png "Protokoly auditu")


Filtr pro **rozsah dat** umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty:

- 1 měsíc
- 7 dní
- 24 hodin
- Vlastní

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncový čas.

Filtr **Spustil(a)** umožňuje definovat jméno aktéra nebo hlavní název uživatele (UPN).

Filtr **Kategorie** umožňuje vybrat jeden z následujících filtrů:

- Vše
- Základní kategorie
- Základní adresář
- Samoobslužná správa hesel
- Samoobslužná správa skupin
- Zřizování účtů – automatická změna hesel
- Pozvaní uživatelé
- Služba MIM
- Identity Protection
- B2C

Filtr **Typ prostředku aktivity** umožňuje vybrat jeden z následujících filtrů:

- Vše 
- Skupina
- Adresář
- Uživatel
- Aplikace
- Zásada
- Zařízení
- Ostatní

Když jako **Typ prostředku aktivity** vyberete **Skupina**, zobrazí se další kategorie filtru, která umožňuje zadat také **Zdroj**:

- Azure AD
- O365


Filtr **Aktivita** je založený na vybrané kategorii a typu prostředku aktivity. Můžete vybrat konkrétní aktivitu, kterou chcete zobrazit, nebo zvolit všechny. 

Seznam všech aktivit auditu můžete získat pomocí Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, kde $tenantdomain = název domény. Také se můžete podívat na článek o [událostech sestavy auditování](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Zástupci pro protokoly auditu

Kromě **Azure Active Directory** poskytuje web Azure Portal dva další vstupní body k datům auditu:

- Uživatelé a skupiny
- Podnikové aplikace

### <a name="users-and-groups-audit-logs"></a>Protokoly auditu uživatelů a skupin

S použitím sestav auditu orientovaných na uživatele a skupiny můžete najít odpovědi na otázky tohoto typu:

- Jaké typy aktualizací uživatelé použili?

- Kolik uživatelů bylo změněno?

- Kolik hesel bylo změněno?

- Co provedl správce v adresáři?

- Které skupiny byly přidány?

- Došlo u některých skupin ke změnám členství?

- Došlo ke změnám vlastníků skupiny?

- Jaké licence byly přiřazeny skupině nebo uživateli?

Pokud chcete jenom zkontrolovat data auditování týkající se uživatelů a skupin, najdete filtrované zobrazení v sekci **Protokoly auditu** v oddílu **Aktivity** v části **Uživatelé a skupiny**. Tento vstupní bod má jako **Typ prostředku aktivity** předem vybranou možnost **Uživatelé a skupiny**.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/93.png "Protokoly auditu")

### <a name="enterprise-applications-audit-logs"></a>Protokoly auditu podnikových aplikací

S použitím sestav auditu orientovaných na aplikace můžete najít odpovědi na otázky tohoto typu:

* Které aplikace byly přidány nebo aktualizovány?
* Které aplikace byly odebrány?
* Změnil se instanční objekt pro aplikaci?
* Změnily se názvy aplikací?
* Kdo udělil souhlas pro aplikaci?

Pokud chcete jenom zkontrolovat data auditování týkající se aplikací, najdete filtrované zobrazení v sekci **Protokoly auditu** v oddílu **Aktivity** v části **Podnikové aplikace**. Tento vstupní bod má jako **Typ prostředku aktivity** předem vybranou možnost **Podniková aplikace**.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/134.png "Protokoly auditu")

Toto zobrazení je možné dál filtrovat až na samotné **skupiny** nebo **uživatele**.

![Protokoly auditu](./media/active-directory-reporting-activity-audit-logs/25.png "Protokoly auditu")



## <a name="next-steps"></a>Další postup

- Přehled generování sestav najdete v tématu [Generování sestav v Azure Active Directory](active-directory-reporting-azure-portal.md).

- Úplný seznam všech aktivit auditu najdete v [referenčních informacích k aktivitám auditování Azure AD](active-directory-reporting-activity-audit-reference.md).


---
title: Známé problémy zřizování aplikací v Azure AD
description: Přečtěte si o známých problémech při práci s automatizovaným zřizováním aplikací v Azure AD.
author: kenwith
ms.author: kenwith
manager: celestedg
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.reviewer: arvinh
ms.openlocfilehash: 2f83679a39f919e5e9932303731560aedd796233
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052375"
---
# <a name="known-issues-application-provisioning"></a>Známé problémy: zřizování aplikací
Známé problémy, které je třeba znát při práci s zřizováním aplikací. Zpětnou vazbu o službě zřizování aplikací na UserVoice najdete v tématu [Azure AD Application zřizování UserVoice](https://aka.ms/appprovisioningfeaturerequest). Úzce sledujeme UserVoice, abychom mohli službu vylepšit. 

> [!NOTE]
> Nejedná se o úplný seznam známých problémů. Pokud víte, který problém není uveden, poskytněte nám zpětnou vazbu v dolní části stránky.

## <a name="authorization"></a>Autorizace 

**Po úspěšném testu připojení se nepovedlo Uložit.**

Pokud můžete připojení úspěšně otestovat, ale nemůžete ho uložit, překročili jste maximální povolený limit úložiště pro přihlašovací údaje. Další informace najdete v tématu [problém při ukládání přihlašovacích údajů správce](application-provisioning-config-problem-storage-limit.md).

**Nejde Uložit.**

Aby se uložila adresa URL tenanta, tajný token a oznamovací e-mail, musí se vyplnit. Nemůžete zadat jenom jeden z nich. 

**Nepovedlo se změnit režim zřizování zpátky na ruční.**

Jakmile nakonfigurujete zřizování poprvé, všimnete si, že režim zřizování byl přepnut z ruční na automatický. Nemůžete ho změnit zpátky na ruční. Můžete ale vypnout zřizování prostřednictvím uživatelského rozhraní. Efektivní vypnutí zřizování v uživatelském rozhraní má stejnou hodnotu jako nastavení rozevíracího seznamu na ruční.  


## <a name="attribute-mappings"></a>Mapování atributů 

**Atribut SamAccountName nebo userType není k dispozici jako zdrojový atribut.**

Atributy SamAccountName a userType nejsou ve výchozím nastavení k dispozici jako zdrojový atribut. Rozšíříte schéma pro přidání atributu. Můžete přidat atributy do seznamu dostupných zdrojových atributů rozšířením schématu. Další informace najdete v tématu [chybějící zdrojový atribut](user-provisioning-sync-attributes-for-mapping.md). 

**Chybí rozevírací seznam zdrojového atributu pro rozšíření schématu.**

Rozšíření schématu mohou někdy chybět v rozevíracím seznamu zdrojového atributu v uživatelském rozhraní. V části Rozšířená nastavení mapování atributů a přidejte atributy ručně. Další informace najdete v tématu [Přizpůsobení mapování atributů](customize-application-attributes.md).

**Atribut s hodnotou null nelze zřídit.**

Služba Azure AD momentálně nemůže zřídit atributy s hodnotou null. Pokud má atribut v uživatelském objektu hodnotu null, bude vynechán. 

**Maximální počet znaků pro výrazy mapování atributů**

Výrazy mapování atributu můžou mít maximálně 10 000 znaků. 


## <a name="service-issues"></a>Potíže se službou 

**Nepodporované scénáře**

- Hesla zřizování se nepodporují. 
- Zřizování vnořených skupin se nepodporuje. 
- Zřizování pro klienty B2C se nepodporuje kvůli velikosti klientů. 

**Automatické zřizování není v naší aplikaci založené na OIDC k dispozici.**

Pokud vytvoříte registraci aplikace, nebude mít odpovídající instanční objekt v podnikových aplikacích povolený automatický zřizování uživatelů. Musíte buď požádat o přidání aplikace do galerie, pokud je určená pro použití ve více organizacích, nebo vytvořit druhou aplikaci mimo galerii pro zřizování. 

**Interval zřizování je opravený.**

[Čas](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user#how-long-will-it-take-to-provision-users) mezi cykly zřizování není v současné době možné konfigurovat. 

**Změny, které se nepohybují z cílové aplikace do Azure AD**

Služba zřizování aplikací neví o změnách provedených v externích aplikacích. Takže se neprovádí žádná akce, která by se vrátila zpět. Služba zřizování aplikací se spoléhá na změny provedené ve službě Azure AD. 

**Cyklus zřizování pokračuje až do dokončení.**

Při nastavování zřizování `enabled = off` nebo zastavení na zastaveno bude aktuální cyklus zřizování pokračovat až do dokončení. Služba zastaví provádění všech budoucích cyklů, dokud znovu nepovolíte zřizování.

**Člen skupiny není zřízený.**

Když je skupina v oboru a člen je mimo rozsah, skupina se zřídí. Uživatel mimo rozsah se zřídí. Pokud se člen vrátí do oboru, služba tuto změnu okamžitě nerozpozná. Tento problém se bude řešit restartováním zřizování. Doporučujeme službu pravidelně restartovat, aby se zajistilo správné zřízení všech uživatelů.  


## <a name="next-steps"></a>Další kroky
- [Jak funguje zřizování](how-provisioning-works.md)

---
title: Azure AD Connect – Správa důvěryhodnosti služby AD FS s Azure AD pomocí služby Azure AD Connect | Dokumentace Microsoftu
description: Provozní údaje o zpracování službou Azure AD vztah důvěryhodnosti Azure AD connect.
keywords: Služba AD FS, AD FS, správy služby AD FS, AAD Connect, připojit, Azure AD, důvěryhodnost mezi doménami, AAD, deklarace identity, deklarace identity, deklarace identity, pravidla, vystavení, transformaci, pravidla, zálohování, obnovení
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.openlocfilehash: 660ded4cd5cfd4da3c04e1044c890612e92ea9c4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55161176"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Správa vztah důvěryhodnosti služby AD FS pomocí Azure AD pomocí služby Azure AD Connect

## <a name="overview"></a>Přehled

Azure AD Connect můžete spravovat federace mezi službami v místním Active Directory Federation Services (AD FS) a Azure AD. Tento článek obsahuje přehled:

* Různá nastavení nakonfigurované na vztah důvěryhodnosti Azure AD Connect
* Nastavte pravidla transformace vystavení (pravidla deklarace identity) pomocí služby Azure AD Connect
* Postup zálohování a obnovení vašeho nároku mezi upgrady a konfigurace pravidla aktualizace. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Nastavení Azure AD Connect

Azure AD Connect spravuje **pouze** související nastavení se vztah důvěryhodnosti Azure AD. Azure AD Connect neprovede žádné změny nastavení na jiné vztahy důvěryhodnosti předávající strany ve službě AD FS. Následující tabulka uvádí nastavení, které jsou řízeny pomocí služby Azure AD Connect.

| Nastavení | Popis |
| :--- | :--- |
| Podpisový certifikát tokenu | Azure AD Connect je možné obnovit a znovu vytvořit vztah důvěryhodnosti s Azure AD. Azure AD Connect provede jednorázovou okamžité výměny podpisové certifikáty tokenů pro službu AD FS a aktualizuje nastavení federace domény Azure AD.|
| Token podpisový algoritmus | Microsoft doporučuje používat jako token podpisový algoritmus SHA-256. Azure AD Connect může zjistit, pokud token podpisový algoritmus je nastavený na hodnotu méně bezpečné než algoritmus SHA-256. V další operaci možné konfigurace ji bude aktualizujte nastavení na SHA-256. Použít nový podpisový certifikát tokenu musí aktualizovat ostatní vztah důvěryhodnosti předávající strany. |
| Identifikátor vztahu důvěryhodnosti Azure AD | Azure AD Connect nastaví hodnotu správný identifikátor pro vztah důvěryhodnosti Azure AD. Služba AD FS jednoznačně identifikuje vztah důvěryhodnosti Azure AD pomocí hodnota identifikátoru. |
| Koncové body Azure AD | Azure AD Connect zajišťuje, že koncové body konfigurované pro vztah důvěryhodnosti Azure AD jsou vždy podle doporučené hodnoty pro výkon a odolnost proti chybám. |
| Pravidla transformace vystavení | Počet pravidel deklarací identity, které jsou potřeba pro zajištění optimálního výkonu funkce služby Azure AD ve federované nastavení nejsou k dispozici. Azure AD Connect zajišťuje, že vztah důvěryhodnosti Azure AD je vždy nakonfigurovanou správnou sadu pravidel deklarací identity doporučené. |
| Alternativní id | Pokud se synchronizace je nakonfigurován pro použití alternativní id, Azure AD Connect konfiguraci služby AD FS provádět ověření pomocí alternativní id. |
| Automatické metadata update | Vztah důvěryhodnosti s Azure AD je nakonfigurovaný pro automatické metadata aktualizací. Služba AD FS pravidelně kontroluje metadata vztahu důvěryhodnosti Azure AD a udržuje je aktuální v případě, že se změní na straně služby Azure AD. |
| Ověření integrované Windows (IWA) | Během operace připojení k hybridní službě Azure AD je povoleno IWA pro registraci zařízení pro usnadnění připojení k hybridní službě Azure AD pro zařízení s nižší úrovně |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Spuštění toků a nastavení federace Azure AD Connect

Služby Azure AD connect neprovede aktualizaci všech nastavení pro vztah důvěryhodnosti Azure AD během konfigurace toků. Změněné nastavení závisí na jaké úlohy nebo spuštění toku se zpracovává. Následující tabulka uvádí nastavení vliv v různých pracovních toků.

| Spuštění toku | Nastavení vliv |
| :--- | :--- |
| Nejdříve projít instalace (rychlé) | Žádný |
| Nejdříve projít instalace (novou farmu služby AD FS) | Vytvořit novou farmu služby AD FS a vztah důvěryhodnosti s Azure AD je vytvořený z nuly. |
| Nejdříve projít instalace (existující farmu služby AD FS, existující vztah důvěryhodnosti Azure AD) | Identifikátor vztahu důvěryhodnosti Azure AD, pravidla transformace vystavení, koncové body služby Azure AD, alternativní id (v případě potřeby), automatické metadata update |
| Resetovat vztah důvěryhodnosti služby Azure AD | Token podpisový certifikát, podpisový algoritmus, identifikátor vztahu důvěryhodnosti Azure AD, transformace vystavení tokenu pravidla, koncové body služby Azure AD, alternativní id (v případě potřeby), automatické metadata update |
| Přidat federační server | Žádný |
| Přidání serveru WAP | Žádný |
| Možnosti zařízení | Pravidla transformace vystavení, IWA pro registraci zařízení |
| Přidání federované domény | Pokud přidáte doméně poprvé, to znamená, nastavení se mění z jedné domény federace do více domény federace – Azure AD Connect vytvoří znovu od začátku vztah důvěryhodnosti. Pokud už je nakonfigurovaný vztah důvěryhodnosti s Azure AD pro více domén, jsou změněny pouze pravidla transformace vystavení |
| Update SSL | Žádný |

Během všechny operace, ve kterém, všechna nastavení je upravený, Azure AD Connect vytvoří zálohu aktuální nastavení vztahu důvěryhodnosti v **%ProgramData%\AADConnect\ADFS**

![Azure AD Connect stránka zobrazující zprávu o existující zálohy vztah důvěryhodnosti Azure AD](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Starší než verze 1.1.873.0 zálohování se skládal z pouze pravidla transformace vystavení a byly zálohovány v souboru protokolu trasování průvodce.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Pravidla transformace vystavení nastavením Azure AD Connect

Azure AD Connect zajišťuje, že vztah důvěryhodnosti Azure AD je vždy nakonfigurovanou správnou sadu pravidel deklarací identity doporučené. Společnost Microsoft doporučuje použití služby Azure AD connect pro správu vašich vztah důvěryhodnosti Azure AD. Tato část obsahuje sada pravidel transformace pro vystavování a jejich popis.

| Název pravidla | Popis |
| --- | --- |
| Problém (UPN) | Toto pravidlo vyhledá hodnota userprincipalname jako z atributu nakonfigurovaného v nastavení synchronizace pro atribut userprincipalname.|
| Dotaz objectguid a msdsconsistencyguid pro vlastní deklarace identity ImmutableId | Toto pravidlo přidá dočasná hodnota v kanálu pro objectguid a msdsconsistencyguid hodnotu, pokud existuje |
| Kontrola existence msdsconsistencyguid | Podle toho, zda existuje hodnota msdsconsistencyguid nebo Ne, nastavíme dočasné příznak pro použití jako ImmutableId přesměrování |
| Pokud existuje vydat msdsconsistencyguid jako neměnné ID | Pokud tato hodnota existuje vydat msdsconsistencyguid jako ImmutableId |
| Pokud neexistuje pravidlo msdsConsistencyGuid vydat objectGuidRule | Pokud hodnota msdsconsistencyguid neexistuje, hodnota objectguid budou vystavené jako ImmutableId |
| Problém nameidentifier | Toto pravidlo vyvolá hodnotu deklarace identity nameidentifier.|
| Problém accounttype pro počítače připojené k doméně | Pokud entita ověřuje je zařízení připojené k doméně, toto pravidlo vydá typ účtu jako DJ značící zařízení připojené k doméně |
| AccountType problém s hodnotou uživatele, pokud není účet počítače | Pokud je entita ověřovaného uživatele, toto pravidlo vydá typ účtu jako uživatel |
| Vydat issuerid, když není účet počítače | Toto pravidlo vydá hodnota issuerId při ověřování entita není zařízení. Hodnota je vytvořen pomocí regulárního výrazu, který je nakonfigurovaný pomocí služby Azure AD Connect. Regulární výraz je vytvořen po vezměte v úvahu všechny domény Federovaná pomocí služby Azure AD Connect. |
| Issuerid problém pro ověření počítače DJ | Toto pravidlo vydá hodnota issuerId zařízení při ověřování entity |
| Problém onpremobjectguid pro počítače připojené k doméně | Pokud entita ověřuje je zařízení připojené k doméně, toto pravidlo vydá objectguid místní zařízení |
| Primární identifikátor SID předávání | Toto pravidlo vydá primární identifikátor SID ověřovací entity |
| Předávání deklarace identity – insideCorporateNetwork | Problémy tato pravidla deklarace identity, která pomáhá Azure AD vědět, pokud ověření pochází z uvnitř podnikové sítě nebo externě |
| Předávání deklarace identity – Psso |   |
| Vydávání deklarací vypršení platnosti hesla | Toto pravidlo problémy tři deklarace pro čas vypršení platnosti hesla, kolik dní hesla vyprší entity ověřuje a adresu URL umístění pro směrování pro změnu hesla.|
| Předávání deklarace identity – authnmethodsreferences | Hodnota v vydané podle tohoto pravidla deklarace identity označuje bylo pro entitu provést, jaký typ ověřování |
| Předávání deklarace identity – multifactorauthenticationinstant | Hodnota této deklarace Určuje dobu, ve standardu UTC, kdy provedl uživatel naposledy více Multi-Factor authentication. |
| Předávání deklarace identity – AlternateLoginID | Toto pravidlo vydá deklaraci identity AlternateLoginID v případě, že ověřování se provádí pomocí alternativního přihlašovacího ID |

> [!NOTE]
> Pravidla deklarací identity pro hlavní název uživatele problém a ImmutableId se bude lišit, pokud používáte jiné než výchozí volbou při konfiguraci služby Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Obnovení pravidla transformace vystavení

Verze služby Azure AD Connect 1.1.873.0 nebo novější Díky zálohování služby Azure AD s důvěrou nastavení pokaždé, když se aktualizace provedené v nastavení vztahu důvěryhodnosti Azure AD. Nastavení vztahu důvěryhodnosti Azure AD jsou zálohované zde **%ProgramData%\AADConnect\ADFS**. Název souboru je v následujícím formátu AadTrust -&lt;datum&gt;-&lt;čas&gt;.txt, například - AadTrust-20180710-150216.txt

![Sanpshot příkladu zálohování vztah důvěryhodnosti Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

Můžete obnovit pomocí následujícího postupu navrhované pravidla transformace při vystavení

1. Ve Správci serveru otevřete uživatelské rozhraní pro správu služby AD FS
2. Otevřete vlastnosti vztahu důvěryhodnosti Azure AD tak, že přejdete **služby AD FS &gt; důvěryhodnosti předávající strany &gt; platforma identit Microsoft Office 365 &gt; upravit zásady vystavování deklarací identity**
3. Klikněte na **přidat pravidlo**
4. V šablony pravidla deklarace identity vyberte možnost odeslat nároky pomocí vlastního pravidla a klikněte na tlačítko **další**
5. Název pravidla deklarace identity z záložní soubor zkopírujte a vložte ho do pole **název pravidla deklarace identity**
6. Kopírovat pravidlo deklarace identity ze záložního souboru do textového pole pro **vlastního pravidla** a klikněte na tlačítko **dokončit**

> [!NOTE]
> Ujistěte se, že další pravidla nejsou v konfliktu s pravidly nakonfigurovaný službou Azure AD Connect.

## <a name="next-steps"></a>Další postup
* [Správa a přizpůsobení Active Directory Federation Services přes Azure AD Connect](how-to-connect-fed-management.md)

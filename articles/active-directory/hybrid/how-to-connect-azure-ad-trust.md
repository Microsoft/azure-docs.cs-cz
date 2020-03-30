---
title: Azure AD Connect – správa vztahu důvěryhodnosti služby AD FS pomocí služby Azure AD Pomocí Azure AD Connect | Dokumenty společnosti Microsoft
description: Provozní podrobnosti zpracování důvěryhodnosti Azure AD pomocí Azure AD připojení.
keywords: Služba AD FS, Služba ADFS, správa služby AD FS, služba AAD Connect, Connect, Azure AD, důvěryhodnost, AAD, deklarace, nárok, pravidla deklarací, vydání, transformace, pravidla, zálohování, obnovení
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
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3e521fb7668305ce511aaddd63ed2cce8dfed0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331727"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Správa vztahu důvěryhodnosti AD FS s Azure AD s využitím Azure AD Connectu

## <a name="overview"></a>Přehled

Azure AD Connect můžete spravovat federaci mezi místní službou AD FS (AD FS) a Azure AD. Tento článek obsahuje přehled:

* Různá nastavení nakonfigurovaná na základě vztahu důvěryhodnosti službou Azure AD Connect
* Pravidla transformace vystavování (pravidla deklarací) nastavená službou Azure AD Connect
* Jak zálohovat a obnovit pravidla deklarací mezi upgrady a aktualizacemi konfigurace. 

## <a name="settings-controlled-by-azure-ad-connect"></a>Nastavení řízená službou Azure AD Connect

Azure AD Connect spravuje **jenom** nastavení související s důvěryhodností Azure AD. Azure AD Connect nezmění žádné nastavení na jiné důvěryhodné strany ve službě AD FS. Následující tabulka označuje nastavení, která jsou řízena službou Azure AD Connect.

| Nastavení | Popis |
| :--- | :--- |
| Podpisový certifikát tokenu | Azure AD Connect se dá použít k resetování a znovu vytvoření vztahu důvěryhodnosti pomocí Azure AD. Azure AD Connect provádí jednorázové okamžité převrácení podpisových certifikátů tokenů pro službu AD FS a aktualizuje nastavení federace domény Azure AD.|
| Algoritmus podepisování tokenů | Společnost Microsoft doporučuje používat SHA-256 jako algoritmus podepisování tokenů. Azure AD Connect můžete zjistit, pokud algoritmus podpisu tokenu je nastavena na hodnotu méně zabezpečené než SHA-256. V další možné operaci konfigurace aktualizuje nastavení na SHA-256. Chcete-li použít nový podpisový certifikát tokenu, musí být aktualizován jiný důvěryhodný certifikát předávající strany. |
| Identifikátor důvěryhodnosti Azure AD | Azure AD Connect nastaví správnou hodnotu identifikátoru pro vztah důvěryhodnosti Azure AD. Služba AD FS jednoznačně identifikuje vztah důvěryhodnosti Azure AD pomocí hodnoty identifikátoru. |
| Koncové body Azure AD | Azure AD Connect zajišťuje, že koncové body nakonfigurované pro vztah důvěryhodnosti Azure AD jsou vždy podle nejnovějších doporučených hodnot odolnosti proti chybám a výkonu. |
| Pravidla transformace vystavování | Existuje několik pravidel deklarace, které jsou potřebné pro optimální výkon funkcí Azure AD ve federované nastavení. Azure AD Connect zajišťuje, že vztah důvěryhodnosti Azure AD je vždy nakonfigurovaný se správnou sadou doporučených pravidel deklarací. |
| Alternativní id | Pokud je synchronizace nakonfigurovaná tak, aby používala alternativní ID, Azure AD Connect nakonfiguruje službu AD FS k provádění ověřování pomocí alternativního ID. |
| Automatická aktualizace metadat | Vztah důvěryhodnosti se službou Azure AD je nakonfigurovaný pro automatickou aktualizaci metadat. Služba AD FS pravidelně kontroluje metadata vztahu důvěryhodnosti Azure AD a udržuje je aktuální pro případ, že se změní na straně Azure AD. |
| Integrované ověřování systému Windows (IWA) | Během operace připojení hybridního Azure AD je IWA povolená pro registraci zařízení, která usnadňuje hybridní připojení Azure AD pro zařízení nižší úrovně |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Spuštění toků a nastavení federace nakonfigurovaných službou Azure AD Connect

Azure AD connect neaktualizuje všechna nastavení pro vztah důvěryhodnosti Azure AD během toků konfigurace. Upravená nastavení závisí na tom, který úloha nebo tok spuštění se provádí. V následující tabulce jsou uvedeny nastavení ovlivněné v různých tocích provádění.

| Tok spuštění | Ovlivněné nastavení |
| :--- | :--- |
| Instalace prvního průchodu (expresní) | Žádný |
| Instalace prvního průchodu (nová farma AD FS) | Vytvoří se nová farma služby AD FS a vytvoří se vztah důvěryhodnosti se službou Azure AD od začátku. |
| Instalace prvního průchodu (existující farma služby AD FS, existující vztah důvěryhodnosti Azure AD) | Identifikátor důvěryhodnosti Azure AD, pravidla transformace vystavování, koncové body Azure AD, alternativní id (v případě potřeby), automatická aktualizace metadat |
| Obnovení vztahu důvěryhodnosti Azure AD | Podpisový certifikát tokenu, algoritmus podpisu tokenu, identifikátor důvěryhodnosti Azure AD, pravidla transformace vystavování, koncové body Azure AD, alternativní id (v případě potřeby), automatická aktualizace metadat |
| Přidání federačního serveru | Žádný |
| Přidat server WAP | Žádný |
| Možnosti zařízení | Pravidla transformace vystavování, IWA pro registraci zařízení |
| Přidání federované domény | Pokud se doména přidává poprvé, to znamená, že se nastavení mění z federace jedné domény na federaci s více doménami – Azure AD Connect znovu vytvoří vztah důvěryhodnosti od začátku. Pokud je vztah důvěryhodnosti s Azure AD už nakonfigurovaný pro více domén, změní se jenom pravidla transformace vystavování. |
| Aktualizovat TLS | Žádný |

Během všech operací, ve kterých je změněno jakékoli nastavení, azure ad connect provede zálohu aktuálního nastavení důvěryhodnosti na **%ProgramData%\AADConnect\ADFS**

![Stránka Azure AD Connect zobrazující zprávu o existujícím zálohování důvěryhodnosti Azure AD](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Před verzí 1.1.873.0 se záloha skládala pouze z pravidel transformace vystavování a byla zálohována v souboru protokolu trasování průvodce.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Pravidla transformace vystavování nastavená službou Azure AD Connect

Azure AD Connect zajišťuje, že vztah důvěryhodnosti Azure AD je vždy nakonfigurovaný se správnou sadou doporučených pravidel deklarací. Microsoft doporučuje používat Azure AD connect pro správu vašeho vztahu důvěryhodnosti Azure AD. V této části jsou uvedena pravidla transformace vystavování a jejich popis.

| Název pravidla | Popis |
| --- | --- |
| Problém UPN | Toto pravidlo dotazuje hodnotu userprincipalname jako z atributu nakonfigurovaného v nastavení synchronizace pro userprincipalname.|
| Dotaz objectguid a msdsconsistencyguid pro vlastní deklaraci ImmutableId | Toto pravidlo přidá do kanálu do kanálu hodnotu objectguid a msdsconsistencyguid, pokud existuje. |
| Zkontrolujte existenci msdsconsistencyguid | Na základě toho, zda hodnota msdsconsistencyguid existuje nebo ne, jsme nastavili dočasnou vlajku na přímé, co použít jako ImmutableId |
| Problém msdsconsistencyguid jako ID neměnné, pokud existuje | Problém msdsconsistencyguid jako ImmutableId, pokud hodnota existuje |
| Problém objectGuidRule, pokud pravidlo msdsConsistencyGuid neexistuje | Pokud hodnota pro msdsconsistencyguid neexistuje, hodnota objectguid bude vydána jako ImmutableId |
| Identifikátor názvu problému | Toto pravidlo vydává hodnotu deklarace názvu.|
| Typ účtu problému pro počítače spojené s doménou | Pokud je ověřovaná entita zařízení mandatážní, toto pravidlo vydá typ účtu jako DJ označující zařízení spojené s doménou |
| Problém AccountType s hodnotou USER, pokud se nejedná o účet počítače | Pokud je ověřovaná entita uživatelem, toto pravidlo vydá typ účtu jako uživatel |
| Problém issueridu, pokud se nejedná o účet počítače | Toto pravidlo vydává hodnotu issuerId, pokud ověřovací entita není zařízení. Hodnota se vytvoří prostřednictvím regulárního výrazu, který je nakonfigurován pomocí Azure AD Connect. Regulární výraz se vytvoří po zohlednění všech domén federovaných pomocí azure ad connect. |
| Vydání issuerid pro dj počítač auth | Toto pravidlo vydává hodnotu issuerId, pokud je ověřovací entita zařízením. |
| Problém onpremobjectguid pro počítače spojené s doménou | Pokud je ověřovaná entita zařízení montovna, toto pravidlo vydá místní objektguid pro zařízení |
| Průchod primárním SID | Toto pravidlo vydává primární SID ověřovací entity. |
| Předat deklaraci - insideCorporateNetwork | Toto pravidlo vydává deklaraci, která pomáhá Azure AD zjistit, jestli ověřování pochází z podnikové sítě nebo externě |
| Předat reklamaci – Psso |   |
| Deklarace vypršení platnosti hesla vydání | Toto pravidlo vydává tři deklarace pro čas vypršení platnosti hesla, počet dní, po které vyprší platnost hesla ověřované entity, a adresu URL, kam se má směrovat pro změnu hesla.|
| Předat deklaraci – authnmethodsreferences | Hodnota v deklaraci vydané podle tohoto pravidla označuje, jaký typ ověřování byl proveden pro entitu. |
| Projít deklarací – multifaktorauthenticationinstant | Hodnota této deklarace určuje čas v utc, kdy uživatel naposledy provedl vícefaktorové ověřování. |
| Projít deklarací - AlternateLoginID | Toto pravidlo vydává deklaraci AlternateLoginID, pokud bylo ověření provedeno pomocí alternativního ID přihlášení. |

> [!NOTE]
> Pravidla deklarací pro hlavní název uživatele vydání a immutableid se budou lišit, pokud použijete nevýchozí volbu během konfigurace Azure AD Connect

## <a name="restore-issuance-transform-rules"></a>Obnovit pravidla transformace vystavování

Azure AD Connect verze 1.1.873.0 nebo novější vytvoří zálohu nastavení důvěryhodnosti Azure AD při každé aktualizaci nastavení důvěryhodnosti Azure AD. Nastavení důvěryhodnosti služby Azure AD jsou zálohována na **%ProgramData%\AADConnect\ADFS**. Název souboru je v následujícím formátu&lt;AadTrust- datum&gt;-&lt;času&gt;.txt, například - AadTrust-20180710-150216.txt

![Snímek obrazovky s příkladem zálohování vztahu důvěryhodnosti Azure AD](./media/how-to-connect-azure-ad-trust/backup.png)

Pravidla transformace vystavování můžete obnovit pomocí níže uvedených doporučených kroků.

1. Otevření nového uhlavního nastavení služby AD FS ve Správci serveru
2. Otevření vlastností vztahu důvěryhodnosti Azure AD tím, že půjdete se vztahy důvěryhodnosti důvěryhodné **strany &gt; služby &gt; AD FS microsoft office 365 identity platformy &gt; upravit zásady vystavování deklarací identity**
3. Klikněte na **Přidat pravidlo**
4. V šabloně pravidla deklarace vyberte Odeslat deklarace pomocí vlastního pravidla a klikněte na **Další.**
5. Zkopírujte název pravidla deklarace pohledávky ze záložního souboru a vložte jej do pole **Název pravidla deklarace**
6. Zkopírujte pravidlo deklarace z záložního souboru do textového pole **pro vlastní pravidlo** a klikněte na **Dokončit**

> [!NOTE]
> Ujistěte se, že vaše další pravidla nejsou v konfliktu s pravidly nakonfigurovanými službou Azure AD Connect.

## <a name="next-steps"></a>Další kroky
* [Správa a přizpůsobení služby AD FS pomocí služby Azure AD Connect](how-to-connect-fed-management.md)

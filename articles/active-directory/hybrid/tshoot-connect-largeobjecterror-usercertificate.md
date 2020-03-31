---
title: Azure AD Connect – chyby LargeObject způsobené atributem userCertificate | Dokumenty společnosti Microsoft
description: Toto téma obsahuje nápravné kroky pro chyby LargeObject způsobené atributem userCertificate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c851b5ef024e6584e6f8c93995208b08a91fbb60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "62095485"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Synchronizace azure ad připojení: zpracování chyb LargeObject způsobených atributem userCertificate

Azure AD vynucuje maximální limit **15** hodnot certifikátu na atribut **userCertificate.** Pokud Azure AD Connect exportuje objekt s více než 15 hodnotami do Azure AD, Azure AD vrátí chybu **LargeObject** se zprávou:

>*"Zřízený objekt je příliš velký. Ořízněte počet hodnot atributů u tohoto objektu. Operace bude zopakována v dalším cyklu synchronizace..."*

Chyba LargeObject může být způsobena jinými atributy ad. Chcete-li potvrdit, že je skutečně způsobenatributem userCertificate, je třeba ověřit objekt buď v místním ad nebo v [Metaverse Search Správce synchronizačních služeb](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Chcete-li získat seznam objektů v tenantovi s chybami LargeObject, použijte jednu z následujících metod:

 * Pokud je váš tenant povolen pro Azure AD Connect Health pro synchronizaci, můžete odkazovat na zprávu [o chybě synchronizace](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync) k dispozici.
 
 * E-mail s oznámením o chybách synchronizace adresářů, který je odeslán na konci každého cyklu synchronizace, obsahuje seznam objektů s chybami LargeObject. 
 * [Karta Operace správce služeb synchronizace](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) zobrazí seznam objektů s chybami LargeObject, pokud klepnete na nejnovější operaci Exportovat do Služby Azure AD.
 
## <a name="mitigation-options"></a>Možnosti zmírnění rizik
Dokud není vyřešena chyba LargeObject, nelze exportovat do služby Azure AD další změny atributů stejného objektu. Chcete-li chybu vyřešit, můžete zvážit následující možnosti:

 * Upgradujte Azure AD Connect na sestavení 1.1.524.0 nebo po. V Azure AD Connect sestavení 1.1.524.0 pravidla synchronizace out-of-box byly aktualizovány na neexportovat atributy userCertificate a userSMIMECertificate, pokud atributy mají více než 15 hodnot. Podrobnosti o tom, jak upgradovat Azure AD Connect, najdete v článku [Azure AD Connect: Upgrade z předchozí verze na nejnovější](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementujte **pravidlo odchozí synchronizace** ve službě Azure AD Connect, která exportuje **hodnotu null namísto skutečných hodnot pro objekty s více než 15 hodnotami certifikátu**. Tato možnost je vhodná, pokud nepožadujete žádnou z hodnot certifikátu, které mají být exportovány do Služby Azure AD pro objekty s více než 15 hodnotami. Podrobnosti o implementaci tohoto pravidla synchronizace naleznete v další části [Implementace pravidla synchronizace pro omezení exportu atributu userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Odebráním hodnot, které vaše organizace nepoužívá, snižte počet hodnot certifikátů v místním objektu služby AD (15 nebo méně). To je vhodné, pokud je nadýmání atributu způsobeno vypršením nebo nepoužitými certifikáty. [Pomocí skriptu PowerShellu, který je k dispozici,](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) můžete použít k vyhledání, zálohování a odstranění certifikátů, jejichž platnost vypršela v místním zařízení AD. Před odstraněním certifikátů doporučujeme ověřit u správců infrastruktury veřejných klíčů ve vaší organizaci.

 * Nakonfigurujte Azure AD Connect tak, aby byl atribut userCertificate exportován do Služby Azure AD. Obecně doporučujeme tuto možnost, protože atribut může být použit službou Microsoft Online Services k povolení konkrétních scénářů. Zejména jde o toto:
    * Atribut userCertificate na objektu User používají klienti Exchange Online a Outlook pro podepisování a šifrování zpráv. Další informace o této funkci naleznete v článku [S/MIME pro podepisování zpráv a šifrování](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * Atribut userCertificate na objektu Computer používá Azure AD k povolení připojení zařízení s Windows 10 pro místní doménu k připojení k Azure AD. Další informace o této funkci najdete v článku [Připojení zařízení spojených s doménou k prostředí Azure AD pro Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementace pravidla synchronizace pro omezení exportu atributu userCertificate
Chcete-li vyřešit chybu LargeObject způsobenou atributem userCertificate, můžete implementovat pravidlo odchozí synchronizace v aplikaci Azure AD Connect, které exportuje **hodnotu null namísto skutečných hodnot pro objekty s více než 15 hodnotami certifikátu**. Tato část popisuje kroky potřebné k implementaci pravidla synchronizace pro **objekty User.** Kroky lze upravit pro **kontaktní** a **počítačové** objekty.

> [!IMPORTANT]
> Export nulové hodnoty odebere hodnoty certifikátu, které byly dříve úspěšně exportovány do služby Azure AD.

Kroky lze shrnout takto:

1. Zakažte plánovač synchronizace a ověřte, zda neprobíhá žádná synchronizace.
3. Vyhledejte existující pravidlo odchozí synchronizace pro atribut userCertificate.
4. Vytvořte požadované pravidlo odchozí synchronizace.
5. Ověřte nové pravidlo synchronizace u existujícího objektu s chybou LargeObject.
6. Použijte nové pravidlo synchronizace na zbývající objekty s chybou LargeObject.
7. Ověřte, že neexistují žádné neočekávané změny čekající na export do služby Azure AD.
8. Exportujte změny do služby Azure AD.
9. Znovu povolte plánovač synchronizace.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. Zakázat plánovač synchronizace a ověřit, že neprobíhá žádná synchronizace.
Ujistěte se, že žádná synchronizace probíhá, když jste uprostřed implementace nového pravidla synchronizace, abyste zabránili nechtěným změnám exportovaným do Azure AD. Zakázání integrovaného plánovače synchronizace:
1. Spusťte relaci Prostředí PowerShell na serveru Azure AD Connect.

2. Zakázat naplánovanou synchronizaci spuštěním rutiny:`Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Předchozí kroky se vztahují jenom pro novější verze (1.1.xxx.x) služby Azure AD Connect s integrovaným plánovačem. Pokud používáte starší verze (1.0.xxx.x) služby Azure AD Connect, která používá Plánovač úloh systému Windows, nebo používáte vlastní plánovač (není běžné) k aktivaci pravidelné synchronizace, je třeba je odpovídajícím způsobem zakázat.

1. Spusťte **Správce synchronizačních služeb** tak, že přejdete na start → Služba synchronizace.

1. Přejděte na kartu **Operace** a potvrďte, že neexistuje žádná operace, jejíž stav *probíhá.*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Krok 2. Vyhledání existujícího pravidla odchozí synchronizace pro atribut userCertificate
Mělo by existovat existující pravidlo synchronizace, které je povolené a nakonfigurované pro export atributu userCertificate pro objekty uživatele do služby Azure AD. Vyhledejte toto pravidlo synchronizace a zjistěte jeho **prioritu** a konfiguraci **filtru oborů:**

1. Spusťte **Editor pravidel synchronizace** tak, že přejdete na START → Editor pravidel synchronizace.

2. Nakonfigurujte vyhledávací filtry s následujícími hodnotami:

    | Atribut | Hodnota |
    | --- | --- |
    | Směr |**Odchozí** |
    | Typ objektu MV |**Person (Osoba)** |
    | Konektor |*název konektoru Azure AD* |
    | Typ objektu konektoru |**Uživatele** |
    | Atribut MV |**userCertificate** |

3. Pokud používáte pravidla synchronizace OOB (out-of-box) na konektor Azure AD k exportu atributu UserCertficiate pro objekty uživatele, měli byste získat zpět pravidlo *"Out to AAD – User ExchangeOnline".*
4. Poznamenejte si hodnotu **priority** tohoto pravidla synchronizace.
5. Vyberte pravidlo synchronizace a klepněte na **tlačítko Upravit**.
6. V rozbalovacím dialogovém okně *Upravit potvrzení vyhrazeného pravidla* klepněte na tlačítko **Ne**. (Nebojte se, nebudeme provádět žádné změny tohoto pravidla synchronizace).
7. Na obrazovce úprav vyberte kartu **Filtr vymezení rozsahu.**
8. Poznamenejte si konfiguraci filtru oboru. Pokud používáte pravidlo synchronizace OOB, měla by existovat přesně **jedna skupina filtrů oboru obsahující dvě klauzule**, včetně:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | Stejné | Uživatel |
    | cloudMastered | Notequal | True |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Krok 3. Vytvoření požadovaného pravidla odchozí synchronizace
Nové pravidlo synchronizace musí mít stejný **filtr oboru** a **vyšší prioritu** než stávající pravidlo synchronizace. Tím je zajištěno, že nové pravidlo synchronizace se vztahuje na stejnou sadu objektů jako existující pravidlo synchronizace a přepíše existující pravidlo synchronizace pro atribut userCertificate. Vytvoření pravidla synchronizace:
1. V Editoru pravidel synchronizace klikněte na tlačítko **Přidat nové pravidlo.**
2. Na **kartě Popis**zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Name (Název) | *Zadejte název* | Například *"Out to AAD – Vlastní přepsání pro userCertificate"* |
    | Popis | *Uveďte popis* | Například *"Pokud userCertificate atribut má více než 15 hodnot, export null."* |
    | Připojený systém | *Výběr konektoru Azure AD* |
    | Typ připojeného systémového objektu | **Uživatele** | |
    | Typ objektu Metaverse | **Osoba** | |
    | Typ propojení | **Připojit** | |
    | Priorita | *Zvolte číslo mezi 1 - 99* | Zvolené číslo nesmí být použito žádným existujícím pravidlem synchronizace a má nižší hodnotu (a tedy vyšší prioritu) než existující pravidlo synchronizace. |

3. Přejděte na kartu **filtru oborů** a implementujte stejný filtr oboru, který používá stávající pravidlo synchronizace.
4. Přeskočte kartu **Pravidla spojení.**
5. Přejděte na kartu **Transformace** a přidejte novou transformaci pomocí následující konfigurace:

    | Atribut | Hodnota |
    | --- | --- |
    | Typ toku |**Expression** |
    | Atribut cíle |**userCertificate** |
    | Atribut zdroje |*Použijte následující výraz*:`IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Kliknutím na tlačítko **Přidat** vytvořte pravidlo synchronizace.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Krok 4. Ověření nového pravidla synchronizace u existujícího objektu s chybou LargeObject
Toto je ověřit, že pravidlo synchronizace vytvořené pracuje správně na existující objekt AD s LargeObject chyba před použitím na jiné objekty:
1. Přejděte na kartu **Operace** ve Správci synchronizačních služeb.
2. Vyberte nejnovější operaci Exportovat do Azure AD a klikněte na jeden z objektů s chybami LargeObject.
3.  Na přeskupovací obrazovce Vlastnosti objektu prostoru konektoru klikněte na tlačítko **Náhled.**
4. Na přeskakovací obrazovce Náhled vyberte **Úplná synchronizace** a klepněte na **Náhled potvrzení**.
5. Zavřete obrazovku Náhled a obrazovku Vlastnosti prostoru konektoru.
6. Přejděte na kartu **Konektory** ve Správci synchronizačních služeb.
7. Klikněte pravým tlačítkem myši na konektor **Azure AD** a vyberte **spustit...**
8. V automaticky otevíraném kroku Spustit konektor vyberte **Krok exportu** a klepněte na tlačítko **OK**.
9. Počkejte na export u Azure AD k dokončení a potvrďte, že není žádná další LargeObject chyba na tento konkrétní objekt.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Krok 5. Použití nového pravidla synchronizace u zbývajících objektů s chybou LargeObject
Po přidání pravidla synchronizace je třeba spustit krok úplné synchronizace na konektoru služby AD:
1. Přejděte na kartu **Konektory** ve Správci synchronizačních služeb.
2. Klikněte pravým tlačítkem myši na konektor **ad** konektor a vyberte **spustit...**
3. V automaticky otevíraném poli Spustit konektor vyberte krok **Úplná synchronizace** a klepněte na tlačítko **OK**.
4. Počkejte na dokončení kroku úplné synchronizace.
5. Pokud máte více než jeden konektor ad, opakujte výše uvedené kroky pro zbývající konektory ad. Obvykle více konektorů jsou vyžadovány, pokud máte více místních adresářů.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Krok 6. Ověřte, že nečekají na export do služby Azure AD žádné neočekávané změny.
1. Přejděte na kartu **Konektory** ve Správci synchronizačních služeb.
2. Klikněte pravým tlačítkem myši na konektor **Azure AD** a vyberte **možnost Hledat mezeru v konektoru**.
3. V automaticky otevíraném-
    1. Nastavte obor na **čekající export**.
    2. Zaškrtněte všechna 3 zaškrtávací políčka, včetně **přidat**, **upravit** a **odstranit**.
    3. Kliknutím na tlačítko **Hledat** vrátíte všechny objekty se změnami čekajícími na export do Služby Azure AD.
    4. Ověřte, zda nedošlo k žádným neočekávaným změnám. Chcete-li zkontrolovat změny pro daný objekt, poklepejte na objekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Krok 7. Export změn do Azure AD
Export ovat změny do Služby Azure AD:
1. Přejděte na kartu **Konektory** ve Správci synchronizačních služeb.
2. Klikněte pravým tlačítkem myši na konektor **Azure AD** a vyberte **spustit...**
4. V automaticky otevíraném kroku Spustit konektor vyberte **Krok exportu** a klepněte na tlačítko **OK**.
5. Počkejte na dokončení exportu do služby Azure AD a potvrďte, že neexistují žádné další chyby LargeObject.

### <a name="step-8-re-enable-sync-scheduler"></a>Krok 8. Opětovné povolení plánovače synchronizace
Teď, když je problém vyřešen, znovu povolte předdefinovaný plánovač synchronizace:
1. Spusťte relaci prostředí PowerShell.
2. Znovu povolte naplánovanou synchronizaci spuštěním rutiny:`Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Předchozí kroky se vztahují jenom pro novější verze (1.1.xxx.x) služby Azure AD Connect s integrovaným plánovačem. Pokud používáte starší verze (1.0.xxx.x) služby Azure AD Connect, která používá Plánovač úloh systému Windows, nebo používáte vlastní plánovač (není běžné) k aktivaci pravidelné synchronizace, je třeba je odpovídajícím způsobem zakázat.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).


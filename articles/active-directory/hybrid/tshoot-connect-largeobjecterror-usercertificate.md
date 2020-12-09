---
title: Chyby Azure AD Connect – LargeObject způsobené atributem userCertificate | Microsoft Docs
description: Toto téma popisuje nápravné kroky pro chyby LargeObject způsobené atributem userCertificate.
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
ms.topic: troubleshooting
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b419e0f24201d661ad0f5f1373022ea6e9e9f
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861744"
---
# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Azure AD Connect synchronizace: zpracování chyb LargeObject způsobených atributem userCertificate

Služba Azure AD vynutila maximální limit **15** hodnot certifikátů v atributu **userCertificate** . Pokud Azure AD Connect exportuje objekt s více než 15 hodnotami do Azure AD, Azure AD vrátí chybu **LargeObject** se zprávou:

>*"Zřízený objekt je moc velký. Ořízne počet hodnot atributu u tohoto objektu. Operace bude opakována v dalším cyklu synchronizace...*

Chyba LargeObject může být způsobena jinými atributy služby AD. Aby bylo možné potvrdit, že je výsledkem atribut userCertificate, je třeba ověřit objekt v místní službě AD nebo v [Synchronization Service Manager ve vyhledávání Metaverse](./how-to-connect-sync-service-manager-ui-mvsearch.md).

Pokud chcete získat seznam objektů ve vašem tenantovi s LargeObject chybami, použijte jednu z následujících metod:

 * Pokud je u tenanta povolený Azure AD Connect Health ke synchronizaci, můžete se podívat na poskytnutou [zprávu o chybách synchronizace](./how-to-connect-health-sync.md) .
 
 * Oznamovací e-mail pro chyby synchronizace adresářů, které se odesílají na konci každého synchronizačního cyklu, má seznam objektů s LargeObject chybami. 
 * Pokud kliknete na nejnovější operaci export do služby Azure AD, na [kartě Synchronization Service Manager operace](./how-to-connect-sync-service-manager-ui-operations.md) se zobrazí seznam objektů s chybami LargeObject.
 
## <a name="mitigation-options"></a>Možnosti zmírnění
Dokud nebude chyba LargeObject vyřešena, nelze do služby Azure AD exportovat jiné změny atributů stejného objektu. Chcete-li vyřešit chybu, můžete zvážit následující možnosti:

 * Upgradujte Azure AD Connect pro sestavení 1.1.524.0 nebo After. V Azure AD Connect sestavování 1.1.524.0 buildu se dopředná pravidla synchronizace pro všechna pole aktualizovala tak, aby neexportoval atributy userCertificate a userSMIMECertificate, pokud atributy mají více než 15 hodnot. Podrobnosti o tom, jak upgradovat Azure AD Connect, najdete v článku [Azure AD Connect: upgrade z předchozí verze na nejnovější verzi](./how-to-upgrade-previous-version.md).

 * Implementujte **Pravidlo odchozí synchronizace** v Azure AD Connect, které exportuje **hodnotu null, a ne skutečné hodnoty pro objekty s více než 15 hodnotami certifikátů**. Tato možnost je vhodná, pokud nepotřebujete exportovat žádnou z hodnot certifikátů do služby Azure AD pro objekty s více než 15 hodnotami. Podrobnosti o tom, jak implementovat toto pravidlo synchronizace, najdete v další části [implementace pravidla synchronizace pro omezení exportu atributu userCertificate](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute).

 * Snižte počet hodnot certifikátů na objektu místní služby AD (15 nebo méně) tím, že odeberete hodnoty, které už vaše organizace nepoužívá. To je vhodné, pokud je atribut dispozici determinističtější způsoben vypršením nebo nepoužitými certifikáty. Pomocí [skriptu prostředí PowerShell, který je zde k dispozici](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) , můžete najít, zálohovat a odstranit certifikáty s vypršenou platností v místní službě AD. Před odstraněním certifikátů se doporučujeme ověřit u správců infrastruktury veřejných klíčů ve vaší organizaci.

 * Nakonfigurujte Azure AD Connect, aby se vyloučil Export atributu userCertificate do služby Azure AD. Obecně platí, že tuto možnost nedoporučujeme, protože atribut můžou použít služby Microsoft Online Services k povolení konkrétních scénářů. Zejména jde o toto:
    * Atribut userCertificate objektu User používá klient Exchange Online a Outlook pro podepisování a šifrování zpráv. Další informace o této funkci najdete v článku [S/MIME pro podepisování a šifrování zpráv](/microsoft-365/security/office-365-security/s-mime-for-message-signing-and-encryption).

    * V Azure AD se používá atribut userCertificate na objektu počítače, aby se zařízení s Windows 10 připojená k doméně mohla připojit k Azure AD. Pokud se o této funkci chcete dozvědět víc, přečtěte si článek [připojení zařízení připojených k doméně k Azure AD pro prostředí Windows 10](../devices/hybrid-azuread-join-plan.md).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementace pravidla synchronizace pro omezení exportu atributu userCertificate
Chcete-li vyřešit chybu LargeObject způsobenou atributem userCertificate, můžete implementovat pravidlo odchozí synchronizace v Azure AD Connect, které exportuje **hodnotu null namísto skutečných hodnot pro objekty s více než 15 hodnotami certifikátů**. Tato část popisuje kroky potřebné k implementaci pravidla synchronizace pro objekty **uživatele** . Tyto kroky lze upravit pro objekty **kontaktů** a **počítačů** .

> [!IMPORTANT]
> Při exportu hodnoty null se úspěšně exportují hodnoty certifikátů do Azure AD.

Postup může být shrnut jako:

1. Zakáže Plánovač synchronizace a ověří, že neprobíhá žádná synchronizace.
3. Vyhledejte stávající pravidlo odchozí synchronizace pro atribut userCertificate.
4. Vytvořte požadované pravidlo odchozí synchronizace.
5. Ověřte nové pravidlo synchronizace u existujícího objektu s chybou LargeObject.
6. Použijte nové pravidlo synchronizace pro zbývající objekty s chybou LargeObject.
7. Ověřte, že neexistují žádné neočekávané změny, které čekají na export do Azure AD.
8. Exportujte změny do služby Azure AD.
9. Znovu povolte Plánovač synchronizace.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Krok 1. Zakázat Plánovač synchronizace a ověřit, zda neprobíhá žádná synchronizace
Zajistěte, aby se žádná synchronizace neprobíhala v průběhu implementace nového pravidla synchronizace, aby se předešlo nezamýšleným změnám exportovaným do Azure AD. Zakázání integrovaného plánovače synchronizace:
1. Spusťte relaci PowerShellu na Azure AD Connectovém serveru.

2. Zakažte naplánovanou synchronizaci spuštěním rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> Předchozí kroky platí jenom pro novější verze (1.1. xxx. x) Azure AD Connect s integrovaným plánovačem. Pokud používáte starší verze (1.0. xxx. x) Azure AD Connect, které používají systém Windows Plánovač úloh nebo používáte vlastní Plánovač (Neběžné) pro aktivaci pravidelné synchronizace, je nutné je podle potřeby zakázat.

1. Spusťte **Synchronization Service Manager** spuštěním synchronizační služby →.

1. Přejít na kartu **operace** a potvrďte, že neexistuje žádná operace, jejíž stav probíhá *.*

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Krok 2. Najít stávající pravidlo odchozí synchronizace pro atribut userCertificate
Mělo by existovat existující synchronizační pravidlo, které je povolené a nakonfigurované pro export atributu userCertificate uživatelských objektů do Azure AD. Vyhledejte toto pravidlo synchronizace, abyste zjistili jeho **prioritu** a konfiguraci **filtru oboru** :

1. Spusťte **Editor synchronizačních pravidel** tak, že otevřete Editor pravidel synchronizace →.

2. Nakonfigurujte filtry hledání s následujícími hodnotami:

    | Atribut | Hodnota |
    | --- | --- |
    | Směr |**Odchozí** |
    | Typ objektu MV |**Person (Osoba)** |
    | Konektor |*název vašeho konektoru služby Azure AD* |
    | Typ objektu konektoru |**uživatelský** |
    | MV – atribut |**userCertificate** |

3. Pokud k exportu atributu userCertficiate pro uživatelské objekty používáte pravidla synchronizace OOB (předdefinovaných) na službu Azure AD Connector, měli byste se vrátit k pravidlu *"odchozí pro AAD – uživatel ExchangeOnline"* .
4. Poznamenejte si hodnotu **priority** tohoto pravidla synchronizace.
5. Vyberte pravidlo synchronizace a klikněte na **Upravit**.
6. V místním dialogovém okně *Upravit potvrzení rezervovaného pravidla* klikněte na **ne**. (Nedělejte si starosti, v tomto pravidle synchronizace nebudeme dělat žádné změny).
7. Na obrazovce pro úpravy vyberte kartu **Filtr oboru** .
8. Poznamenejte si konfiguraci filtru oborů. Pokud používáte pravidlo synchronizace OOB, měl by existovat přesně **jedna skupina filtru oborů obsahující dvě klauzule**, včetně:

    | Atribut | Operátor | Hodnota |
    | --- | --- | --- |
    | sourceObjectType | VÝŠI | Uživatel |
    | cloudMastered | NOTEQUAL | Pravda |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Krok 3. Vytvořit požadované pravidlo pro odchozí synchronizaci
Nové pravidlo synchronizace musí mít stejný **Filtr oboru** a **vyšší prioritu** než stávající pravidlo synchronizace. Tím se zajistí, že se nové pravidlo synchronizace bude vztahovat na stejnou sadu objektů jako stávající pravidlo synchronizace a přepíše stávající pravidlo synchronizace pro atribut userCertificate. Postup vytvoření pravidla synchronizace:
1. V editoru pravidel synchronizace klikněte na tlačítko **Přidat nové pravidlo** .
2. Na **kartě Popis** zadejte následující konfiguraci:

    | Atribut | Hodnota | Podrobnosti |
    | --- | --- | --- |
    | Název | *Zadat název* | Například *"odchozí AAD – vlastní přepsání pro userCertificate"* |
    | Popis | *Zadejte popis.* | Například *"Pokud má atribut userCertificate více než 15 hodnot, exportujte hodnotu null."* |
    | Připojený systém | *Vyberte konektor Azure AD.* |
    | Typ připojeného systémového objektu | **uživatelský** | |
    | Typ objektu úložiště metaverse | **uživateli** | |
    | Typ odkazu | **Join** (Spojení) | |
    | Priorita | *Zvolit číslo mezi 1-99* | Zvolené číslo nesmí být použito žádným existujícím pravidlem synchronizace a má nižší hodnotu (a proto vyšší prioritu) než stávající pravidlo synchronizace. |

3. Přejít na kartu **Filtr oboru** a implementovat stejný filtr oboru, který používá existující synchronizační pravidlo.
4. Přeskočit kartu **pravidla spojení** .
5. Chcete-li přidat novou transformaci pomocí následující konfigurace, otevřete kartu **transformace** :

    | Atribut | Hodnota |
    | --- | --- |
    | Typ toku |**Výraz** |
    | Cílový atribut |**userCertificate** |
    | Zdrojový atribut |*Použijte následující výraz*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Kliknutím na tlačítko **Přidat** vytvořte pravidlo synchronizace.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Krok 4: Ověření nového pravidla synchronizace u existujícího objektu s chybou LargeObject
K tomu je potřeba ověřit, jestli vytvořené pravidlo synchronizace funguje správně u existujícího objektu AD s chybou LargeObject, než ho použijete na jiné objekty:
1. V Synchronization Service Manager otevřete kartu **operace** .
2. Vyberte nejnovější operaci export do služby Azure AD a klikněte na jeden z objektů s LargeObject chybami.
3.  V místní obrazovce vlastnosti objektu prostoru konektoru klikněte na tlačítko **Náhled** .
4. V místní obrazovce Preview vyberte **Úplná synchronizace** a klikněte na **Potvrdit náhled**.
5. Zavřete obrazovku Preview a obrazovku vlastnosti objektu prostoru konektoru.
6. V Synchronization Service Manager otevřete kartu **konektory** .
7. Klikněte pravým tlačítkem na konektor **Azure AD** a vyberte **Spustit...**
8. V automaticky otevíraném okně konektoru spustit vyberte krok **Export** a klikněte na **OK**.
9. Počkejte na dokončení exportu do Azure AD a potvrďte, že u tohoto konkrétního objektu není k dispozici žádná další LargeObject chyba.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Krok 5. Použití nového pravidla synchronizace pro zbývající objekty s chybou LargeObject
Po přidání pravidla synchronizace musíte na konektoru služby AD spustit úplný krok synchronizace:
1. V Synchronization Service Manager otevřete kartu **konektory** .
2. Klikněte pravým tlačítkem na konektor **AD** a vyberte **Spustit...**
3. V automaticky otevíraném okně Spustit konektor vyberte možnost **úplný krok synchronizace** a klikněte na tlačítko **OK**.
4. Počkejte na dokončení kroku úplné synchronizace.
5. Pokud máte více konektorů služby AD, opakujte výše uvedené kroky u zbývajících konektorů služby AD. V případě, že máte více místních adresářů, je obvykle vyžadováno více konektorů.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Krok 6. Ověřte, že neexistují žádné neočekávané změny, které čekají na export do Azure AD.
1. V Synchronization Service Manager otevřete kartu **konektory** .
2. Klikněte pravým tlačítkem na konektor **Azure AD** a vyberte **Hledat místo v konektoru**.
3. V rozbalovacím seznamu prostor vyhledávacího konektoru:
    1. Nastavte rozsah na **Export čeká na vyřízení**.
    2. Zaškrtněte všechna 3 zaškrtávací políčka, včetně **Přidat**, **Upravit** a **Odstranit**.
    3. Klikněte na tlačítko **Hledat** a vraťte všechny objekty se změnami, které čekají na export do Azure AD.
    4. Ověřte, že nedošlo k neočekávaným změnám. Chcete-li prostudovat změny pro daný objekt, dvakrát klikněte na objekt.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Krok 7. Exportujte změny do Azure AD.
Export změn do Azure AD:
1. V Synchronization Service Manager otevřete kartu **konektory** .
2. Klikněte pravým tlačítkem na konektor **Azure AD** a vyberte **Spustit...**
4. V automaticky otevíraném okně konektoru spustit vyberte krok **Export** a klikněte na **OK**.
5. Počkejte na dokončení exportu do Azure AD a potvrďte, že nejsou k dispozici žádné další LargeObject chyby.

### <a name="step-8-re-enable-sync-scheduler"></a>Krok 8. Opětovné povolení plánovače synchronizace
Teď, když se problém vyřeší, znovu povolte integrovaný Plánovač synchronizace:
1. Spusťte relaci PowerShellu.
2. Znovu povolit plánovanou synchronizaci spuštěním rutiny: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> Předchozí kroky platí jenom pro novější verze (1.1. xxx. x) Azure AD Connect s integrovaným plánovačem. Pokud používáte starší verze (1.0. xxx. x) Azure AD Connect, které používají systém Windows Plánovač úloh nebo používáte vlastní Plánovač (Neběžné) pro aktivaci pravidelné synchronizace, je nutné je podle potřeby zakázat.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

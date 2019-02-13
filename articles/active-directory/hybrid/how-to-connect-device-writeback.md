---
title: 'Azure AD Connect: Povolení zpětného zápisu zařízení | Dokumentace Microsoftu'
description: Tento dokument podrobně popisuje, jak povolit zpětný zápis zařízení pomocí služby Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ed6fd0a4a1de3fb02b3d8583c0e5c0cecac211
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205507"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Povolení zpětného zápisu zařízení
> [!NOTE]
> Předplatné služby Azure AD Premium je vyžadován pro zpětný zápis zařízení.
> 
> 

Následující dokumentace obsahuje informace o tom, jak povolit funkci zpětného zápisu zařízení ve službě Azure AD Connect. Zpětný zápis zařízení se používá v následujících scénářích:

* Zapnout zásady podmíněného přístupu podle zařízení do služby AD FS (2012 R2 nebo vyšší) chráněné aplikace (vztahy důvěryhodnosti předávající strany).

To poskytuje dodatečné zabezpečení a záruky, které je udělen přístup k aplikacím jenom pro důvěryhodná zařízení. Další informace o podmíněném přístupu najdete v tématu [řízením rizik pomocí podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) a [nastavení místního podmíněného přístupu pomocí Azure Active Directory Device Registration](../../active-directory/active-directory-device-registration-on-premises-setup.md).

> [!IMPORTANT]
> <li>Zařízení se musí nacházet ve stejné doménové struktuře jako uživatelé. Vzhledem k tomu, že zařízení je možné zpětně zapsat do jedné doménové struktury, tato funkce aktuálně nepodporují nasazení s více doménovými strukturami uživatele.</li>
> <li>Objekt konfigurace registrace pouze jedno zařízení můžete přidat do místní doménové struktuře služby Active Directory. Tato funkce není kompatibilní s topologií, kde se v místní službě Active Directory synchronizuje s více adresářů Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Část 1: Instalace služby Azure AD Connect
Nainstalujte Azure AD Connect s použitím vlastní nebo expresní nastavení. Společnost Microsoft doporučuje začít s všichni uživatelé a skupiny se úspěšně synchronizovaly před povolení zpětného zápisu zařízení.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Část 2: Povolení zpětného zápisu zařízení ve službě Azure AD Connect
1. Znovu spusťte Průvodce instalací. Vyberte **konfigurovat možnosti zařízení** z další úkoly stránky a klikněte na tlačítko **Další**. 

    ![Konfigurovat možnosti zařízení](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > K dispozici pouze ve verzi 1.1.819.0 a novějších verzí se nakonfigurovat možnosti zařízení.

2. Na stránce Možnosti zařízení vyberte **nakonfigurovat zpětný zápis zařízení**. Možnost **zakázat zpětný zápis zařízení** nebude k dispozici, dokud nebude povolen zpětný zápis zařízení. Klikněte na **Další** přesunout na další stránku průvodce.
    ![Zvolit operaci zařízení](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na stránce zpětný zápis se zobrazí zadané domény jako doménová struktura zpětný zápis zařízení výchozí.
   ![Vlastní instalace zařízení zpětný zápis cílové doménové struktuře](./media/how-to-connect-device-writeback/writebackforest.png)

4. **Kontejner zařízení** stránka poskytuje možnost přípravy služby active directory pomocí jedné z dostupných možností:

    a. **Zadání podnikových přihlašovacích údajů správce**: Pokud přihlašovací údaje podnikového správce pro doménovou strukturu, kdy je potřeba zpětný zápis zařízení jsou služby Azure AD Connect se připravit doménovou strukturu automaticky během konfigurace zpětného zápisu zařízení.

    b. **Stáhnout Poweshellový skript**: Azure AD Connect automaticky vygeneruje skript Powershellu, který může připravit služby active directory pro zpětný zápis zařízení. V případě, že přihlašovací údaje podnikového správce nelze zadat ve službě Azure AD Connect, doporučuje se stáhnout skript prostředí PowerShell. Zadejte staženého skriptu prostředí PowerShell **CreateDeviceContainer.psq** do doménové struktury, ve kterém zařízení se zapíšou zpátky do správce rozlehlé sítě.
    ![Příprava doménové struktury služby active directory](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Tyto operace jsou prováděny při přípravě doménové struktury služby active directory:
    * Pokud již neexistuje, vytvoří a nakonfiguruje nové kontejnery a objekty v rámci CN = Device Registration Configuration, CN = Services, CN = Configuration, [rozlišující název doménové struktury].
    * Pokud již neexistuje, vytvoří a nakonfiguruje nové kontejnery a objekty v rámci CN = RegisteredDevices, [rozlišující název domény]. Objekty zařízení se vytvoří v tomto kontejneru.
    * Nastaví potřebná oprávnění pro účet konektoru služby Azure AD, ke správě zařízení ve službě Active Directory.
    * Musí spustit jen v jedné doménové struktuře, i v případě, že probíhá instalace služby Azure AD Connect ve více doménových strukturách.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Ověřte, že zařízení se synchronizují do služby Active Directory
Zpětný zápis zařízení by měl nyní pracuje správně. Mějte na paměti, že může trvat až 3 hodiny objektů zařízení, aby byly zapsány zpět do služby AD.  Pokud chcete ověřit, že vaše zařízení se synchronizuje správně, postupujte takto po dokončení synchronizační pravidla:

1. Spusťte Centrum správy služby Active Directory.
2. Rozbalte RegisteredDevices v doméně, která je Federovaná.

   ![Centrum správy služby Active Directory zaregistrované zařízení](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Aktuální registrovaná zařízení budou uvedeny zde.

   ![Centrum správy služby Active Directory zaregistrované seznam zařízení](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Povolení podmíněného přístupu
Podrobné pokyny, jak povolit tento scénář jsou dostupné v rámci [nastavení místního podmíněného přístupu pomocí Azure Active Directory Device Registration](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>Řešení potíží
### <a name="the-writeback-checkbox-is-still-disabled"></a>Zpětný zápis zaškrtávací políčko je stále zakázáno
Pokud zaškrtávací políčko pro zpětný zápis zařízení není povolené, i když jste postupovali podle výše uvedených kroků, následující postup vám pomohou co Průvodce instalací ověřuje před povolením pole.

Nejdřív to nejdůležitější první:

* Doménová struktura, kde jsou k dispozici zařízení musí mít schéma doménové struktury upgradovat na úroveň Windows 2012 R2 tak, že objekt zařízení a přidružených atributů jsou k dispozici.
* Pokud Průvodce instalací je již spuštěna, nebudou zjištěna žádné změny. V tomto případě dokončit Průvodce instalací a potom ho spusťte znovu.
* Ujistěte se, že účet, který jste zadali v inicializačního skriptu je ve skutečnosti správné uživatelské používají konektor služby Active Directory. Chcete-li to ověřit, postupujte takto:
  * Z nabídky start otevřít **synchronizační služba**.
  * Otevřít **konektory** kartu.
  * Najít požadovaný konektor s typem Active Directory Domain Services a vyberte ji.
  * V části **akce**vyberte **vlastnosti**.
  * Přejděte na **připojit k doménové struktuře služby Active Directory**. Ověřte, že doména a uživatelské jméno na této obrazovce shodu určen účet, který jste ke skriptu.
    ![Účet správce služeb synchronizace v konektoru](./media/how-to-connect-device-writeback/connectoraccount.png)

Ověření konfigurace ve službě Active Directory:

* Ověřte, že službu Device Registration Service nachází v následujícím umístění (CN DeviceRegistrationService, CN = = služby registrace zařízení, CN = Device Registration Configuration, CN = Services, CN = Configuration) v názvovém kontextu konfigurace.

![Řešení potíží s DeviceRegistrationService v oboru názvů configuration](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Ověřte, že existuje pouze jeden objekt konfigurace tak, že konfigurace oboru názvů. Pokud existuje více než jeden, odstraňte duplicitní.

![Řešení potíží, vyhledejte je duplicitní](./media/how-to-connect-device-writeback/troubleshoot2.png)

* U objektu Device Registration Service Ujistěte se, že atribut msDS-DeviceLocation je k dispozici a hodnotu. Vyhledávání toto umístění a ujistěte se, že je k dispozici s objectType msDS-DeviceContainer.

![Řešení potíží s msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Řešení potíží s RegisteredDevices třídy objektu](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Ověřte, že má účet používaný službou konektoru služby Active Directory má požadovaná oprávnění ke kontejneru registrovaná zařízení, zjištěných aplikací v předchozím kroku. Toto je očekávané oprávnění k tomuto kontejneru:

![Řešení potíží, ověřte oprávnění pro kontejner](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Ověřte účet služby Active Directory má oprávnění na CN = Device Registration Configuration, CN = Services, CN = objekt konfigurace.

![Řešení potíží, ověřte oprávnění pro konfiguraci registrace zařízení](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Další informace
* [Řízení rizik pomocí podmíněného přístupu](../active-directory-conditional-access-azure-portal.md)
* [Nastavení místního podmíněného přístupu pomocí Azure Active Directory Device Registration](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).


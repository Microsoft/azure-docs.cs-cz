---
title: 'Azure AD Connect: Povolení zpětného zápisu zařízení | Dokumenty společnosti Microsoft'
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
ms.openlocfilehash: 632f6f80184c6ba3409bd30ae070cbaefc77f036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109504"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Povolení zpětného zápisu zařízení
> [!NOTE]
> Předplatné Azure AD Premium je vyžadováno pro zpětný zápis zařízení.
> 
> 

Následující dokumentace obsahuje informace o tom, jak povolit funkci zpětného zápisu zařízení v Azure AD Connect. Zpětný zápis zařízení se používá v následujících scénářích:

* Povolení [windows hello pro firmy pomocí nasazení vztahu důvěryhodnosti hybridních certifikátů](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Povolte podmíněný přístup na základě zařízení do aplikací chráněných službou ADFS (2012 R2 nebo vyšší) (vztahy důvěryhodnosti předávající strany).

To poskytuje další zabezpečení a jistotu, že přístup k aplikacím je uděloven pouze důvěryhodným zařízením. Další informace o podmíněném přístupu najdete [v tématu Správa rizik pomocí podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) a [nastavení místního podmíněného přístupu pomocí registrace zařízení Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md).

> [!IMPORTANT]
> <li>Zařízení musí být umístěna ve stejné doménové struktuře jako uživatelé. Vzhledem k tomu, že zařízení musí být zapsána zpět do jedné doménové struktury, tato funkce v současné době nepodporuje nasazení s více uživatelskými doménovými strukturami.</li>
> <li>Do místní doménové struktury služby Active Directory lze přidat pouze jeden objekt konfigurace registrace zařízení. Tato funkce není kompatibilní s topologii, kde je místní služba Active Directory synchronizována s více adresáři Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Část 1: Instalace služby Azure AD Connect
Nainstalujte Azure AD Connect pomocí vlastního nebo expresního nastavení. Společnost Microsoft doporučuje začít se všemi uživateli a skupinami úspěšně synchronizovány před povolením zpětného zápisu zařízení.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Část 2: Povolení zpětného zápisu zařízení ve službě Azure AD Connect
1. Spusťte průvodce instalací znovu. Na stránce Další úkoly vyberte **Konfigurovat možnosti zařízení** a klepněte na tlačítko **Další**. 

    ![Konfigurace možností zařízení](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Nové možnosti konfigurovat zařízení je k dispozici pouze ve verzi 1.1.819.0 a novější.

2. Na stránce možností zařízení vyberte **Konfigurovat zpětný zápis zařízení**. Možnost **zakázat zpětný zápis zařízení** nebude k dispozici, dokud nebude povolen zpětný zápis zařízení. Kliknutím na **Tlačítko Další** přejděte na další stránku průvodce.
    ![Zvolit provoz zařízení](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Na stránce zpětného zápisu uvidíte zadanou doménu jako výchozí doménovou strukturu zpětného zápisu zařízení.
   ![Cílová doménová struktura zpětného zápisu vlastní instalace zařízení](./media/how-to-connect-device-writeback/writebackforest.png)

4. **Stránka kontejneru zařízení** poskytuje možnost přípravy aktivního adresáře pomocí jedné ze dvou dostupných možností:

    a. **Zadejte pověření správce rozlehlé sítě**: Pokud jsou k dispozici pověření podnikového správce pro doménovou strukturu, kde je potřeba zařízení odepsat zpět, Azure AD Connect připraví doménovou strukturu automaticky během konfigurace zpětného zápisu zařízení.

    b. **Stáhnout skript PowerShellu**: Azure AD Connect automaticky generuje skript Prostředí PowerShell, který může připravit službu Active Directory na zpětný zápis zařízení. V případě, že pověření podnikového správce nelze poskytnout v Azure AD Connect, doporučujeme stáhnout skript PowerShellu. Zadejte stažený skript Prostředí PowerShell **CreateDeviceContainer.psq** podnikovému správci doménové struktury, do které budou zařízení zapsána zpět.
    ![Příprava doménové struktury služby Active Directory](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Pro přípravu doménové struktury služby Active Directory se provádějí následující operace:
    * Pokud ještě neexistují, vytvoří a nakonfiguruje nové kontejnery a objekty v rámci CN=Konfigurace registrace zařízení,CN=Services,CN=Configuration,[forest-dn].
    * Pokud ještě neexistují, vytvoří a nakonfiguruje nové kontejnery a objekty v rámci CN=RegisteredDevices,[domain-dn]. Objekty zařízení budou vytvořeny v tomto kontejneru.
    * Nastaví potřebná oprávnění pro účet Konektor Azure AD pro správu zařízení ve službě Active Directory.
    * Jenom jepotřeba spustit v jedné doménové struktuře, i v případě, že Azure AD Connect se instaluje na více doménových struktur.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Ověření synchronizace zařízení se službou Active Directory
Zpětný zápis zařízení by nyní měl pracovat správně. Uvědomte si, že může trvat až 3 hodiny pro objekty zařízení, které mají být zapsány zpět do ad.  Chcete-li ověřit, zda jsou vaše zařízení správně synchronizována, postupujte po dokončení pravidel synchronizace následujícím postupem:

1. Spusťte Centrum správy služby Active Directory.
2. Rozbalte RegisteredDevices v rámci domény, která je federována.

   ![Registrovaná zařízení Centra pro správu služby Active Directory](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Zde budou uvedena aktuální registrovaná zařízení.

   ![Seznam registrovaných zařízení Centra pro správu služby Active Directory](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Povolení podmíněného přístupu
Podrobné pokyny k povolení tohoto scénáře jsou k dispozici v části [Nastavení místního podmíněného přístupu pomocí registrace zařízení Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>Řešení potíží
### <a name="the-writeback-checkbox-is-still-disabled"></a>Zaškrtávací políčko zpětného zápisu je stále zakázáno.
Pokud zaškrtávací políčko pro zpětný zápis zařízení není povoleno, i když jste postupovali podle výše uvedených kroků, následující kroky vás provedou tím, co průvodce instalací ověřuje před povolením pole.

První věc, první:

* Doménová struktura, ve které jsou zařízení přítomna, musí mít schéma doménové struktury upgradované na úroveň Windows 2012 R2 tak, aby byl přítomen objekt zařízení a přidružené atributy .
* Pokud je průvodce instalací již spuštěn, nebudou zjištěny žádné změny. V takovém případě dokončete průvodce instalací a spusťte jej znovu.
* Ujistěte se, že účet, který zadáte ve skriptu inicializace, je ve skutečnosti správným uživatelem používaným konektorem služby Active Directory. Chcete-li to ověřit, postupujte takto:
  * V nabídce Start otevřete **položku Synchronizační služba**.
  * Otevřete kartu **Konektory.**
  * Vyhledejte konektor s typem Služby Domény služby Active Directory a vyberte jej.
  * V části **Akce**vyberte **Vlastnosti**.
  * Přejděte na **odkaz Připojit k doménové struktuře služby Active Directory**. Ověřte, zda doména a uživatelské jméno zadané na této obrazovce odpovídají účtu poskytnutému skriptu.
    ![Účet konektoru ve Správci synchronizačních služeb](./media/how-to-connect-device-writeback/connectoraccount.png)

Ověřte konfiguraci ve službě Active Directory:

* Ověřte, zda je služba registrace zařízení umístěna v níže uvedeném umístění (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) v kontextu pojmenování konfigurace.

![Poradce při potížích, DeviceRegistrationService v oboru názvů konfigurace](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Prohledávat obor názvů konfigurace ověřte, zda existuje pouze jeden konfigurační objekt. Pokud existuje více než jeden, odstraňte duplikát.

![Poradce při potížích, hledání duplicitních objektů](./media/how-to-connect-device-writeback/troubleshoot2.png)

* V objektu Služba registrace zařízení zkontrolujte, zda je atribut msDS-DeviceLocation přítomen a má hodnotu. Vyhledejte toto umístění a ujistěte se, že je k dispozici s objectType msDS-DeviceContainer.

![Poradce při potížích, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Poradce při potížích, třída objektů RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Ověřte, zda účet používaný konektorem služby Active Directory má požadovaná oprávnění pro kontejner Registrovaných zařízení nalezený v předchozím kroku. Toto je očekávaná oprávnění pro tento kontejner:

![Poradce při potížích, ověření oprávnění ke kontejneru](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Ověřte, zda má účet Služby Active Directory oprávnění k objektu CN=Device Registration Configuration,CN=Services,CN=Configuration.

![Poradce při potížích, ověření oprávnění v konfiguraci registrace zařízení](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Další informace
* [Řízení rizik s podmíněným přístupem](../active-directory-conditional-access-azure-portal.md)
* [Nastavení místního podmíněného přístupu pomocí registrace zařízení Azure Active Directory](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).


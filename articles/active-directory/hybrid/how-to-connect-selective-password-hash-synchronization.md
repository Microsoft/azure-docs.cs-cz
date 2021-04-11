---
title: Synchronizace hodnot hash selektivního hesla pro Azure AD Connect
description: Tento článek popisuje, jak nastavit a nakonfigurovat synchronizaci hodnot hash selektivního hesla pro použití s Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a73f4eba9581965470b95111e6dda1d8014e4cb
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167494"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Konfigurace synchronizace hodnot hash selektivního hesla pro Azure AD Connect

[Synchronizace hodnot hash hesel](whatis-phs.md) je jednou z metod přihlášení, které se používají k provádění hybridní identity. Azure AD Connect synchronizuje hodnotu hash hesla uživatele z místní instance služby Active Directory s využitím cloudové instance služby Azure AD.  Ve výchozím nastavení se synchronizace hodnot hash hesla projeví u všech synchronizovaných uživatelů.

Pokud byste chtěli mít podmnožinu uživatelů vyloučených z synchronizace hodnoty hash hesla pro službu Azure AD, můžete nakonfigurovat selektivní synchronizaci hodnot hash hesel pomocí kroků uvedených v tomto článku.

>[!Important]
> Společnost Microsoft nepodporuje úpravu ani provozní Azure AD Connect synchronizaci mimo konfigurace nebo akce, které jsou zdokumentovány formálně. Kterákoli z těchto konfigurací nebo akcí může způsobit nekonzistentní nebo nepodporovaný stav Azure AD Connect synchronizace. V důsledku toho společnost Microsoft nemůže zaručit, že bude pro taková nasazení možné poskytovat účinnou technickou podporu. 


## <a name="consider-your-implementation"></a>Zvažte implementaci  
Aby se snížila intenzita správy konfigurace, měli byste nejdřív zvážit počet objektů uživatele, které chcete vyloučit z synchronizace hodnot hash hesel. Ověřte, které z těchto scénářů se vzájemně vylučují, zarovnává se s vašimi požadavky a vyberte správnou možnost konfigurace.
- Pokud je počet uživatelů, kteří mají být **vyloučeni** , **menší** než počet uživatelů, kteří mají **být zahrnuti**, postupujte podle kroků v této [části](#excluded-users-is-smaller-than-included-users).
- Pokud je počet uživatelů, kteří mají být **vyloučeni** , **větší** než počet uživatelů, kteří mají **být zahrnuti**, postupujte podle kroků v této [části](#excluded-users-is-larger-than-included-users).

> [!Important]
> V případě zvolené možnosti konfigurace se při použití změn požadovaná počáteční synchronizace (Úplná synchronizace) provede automaticky v dalším cyklu synchronizace.

> [!Important]
> Konfigurace selektivní synchronizace hodnot hash hesla přímo ovlivňuje zpětný zápis hesla. Změny hesel nebo resetování hesla, které se iniciují v Azure Active Directory zapisují zpátky do místní služby Active Directory jenom v případě, že je uživatel v oboru pro synchronizaci hodnot hash hesel. 

### <a name="the-admindescription-attribute"></a>Atribut adminDescription
Oba scénáře spoléhají na nastavení atributu adminDescription uživatelů na určitou hodnotu.  To umožňuje použít pravidla a je to, co dělá selektivní KOSMETICE práce.

|Scenario|hodnota adminDescription|
|-----|-----|
|Vyloučení uživatelé jsou menší než zahrnutí uživatelé.|PHSFiltered|
|Vyloučení uživatelé je větší než zahrnutí uživatelé.|PHSIncluded|

Tento atribut lze nastavit buď:

- použití uživatelského rozhraní uživatelé a počítače služby Active Directory
- pomocí `Set-ADUser` rutiny prostředí PowerShell.  Další informace najdete v tématu [set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser).

 


### <a name="disable-the-synchronization-scheduler"></a>Zakažte Plánovač synchronizace:
Než začnete s některým z těchto scénářů, je nutné při provádění změn v pravidlech synchronizace zakázat Plánovač synchronizace.
 1. Spusťte Windows PowerShell ENTER.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  Ověřte, že je Plánovač zakázaný, spuštěním následující rutiny:
     
    ```Get-ADSyncScheduler```

Další informace o plánovači najdete v tématu [Azure AD Connect Scheduler Synchronization](how-to-connect-sync-feature-scheduler.md).




## <a name="excluded-users-is-smaller-than-included-users"></a>Vyloučení uživatelé jsou menší než zahrnutí uživatelé.
Následující část popisuje, jak povolit synchronizaci hodnot hash selektivního hesla v případě, že počet uživatelů, kteří mají být **vyloučeni** , je **menší** než počet uživatelů, které chcete **Zahrnout**.

>[!Important]
> Než budete pokračovat, zajistěte, aby byl Plánovač synchronizace zakázán, jak je popsáno výše.

- Vytvořte upravitelnou kopii **v v rámci služby AD – uživatel AccountEnabled** s možností **Povolit zrušit synchronizaci hodnot hash hesel** a definovat svůj filtr oborů. 
- Vytvořte další upravitelnou kopii výchozího nastavení **v z možnosti AD – uživatel AccountEnabled** s možností **Povolit synchronizaci hodnot hash hesel vybranou** a definovat filtr oboru. 
- Opětovné povolení plánovače synchronizace 
- Nastavte hodnotu atributu ve službě Active Directory, která byla definována jako atribut oboru pro uživatele, které mají být povoleny při synchronizaci hodnot hash hesel. 

>[!Important]
>Kroky pro konfiguraci selektivní synchronizace hodnot hash hesel budou mít vliv jenom na uživatelské objekty, které mají atribut **adminDescription** vyplněný ve službě Active Directory hodnotou **PHSFiltered**.
Pokud tento atribut není naplněný nebo je hodnota jiná než **PHSFiltered** , tato pravidla nebudou použita pro objekty uživatele.


### <a name="configure-the-necessary-synchronization-rules"></a>Nakonfigurujte nezbytná pravidla synchronizace:

 1. Spusťte Editor pravidel synchronizace a nastavte filtry pro **synchronizaci hesel** na **zapnuto** a **Typ pravidla** na **Standard**.
     ![Spustit Editor pravidel synchronizace](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Vyberte pravidlo **ve službě AD – uživatel AccountEnabled** pro konektor doménové struktury služby Active Directory, pro který chcete nakonfigurovat selektivní heslo, a klikněte na **Upravit**. V dalším dialogovém okně vyberte **Ano** , pokud chcete vytvořit upravitelnou kopii původního pravidla.
     ![Vybrat pravidlo](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. První pravidlo zakáže synchronizaci hodnot hash hesel. Zadejte následující název nového vlastního pravidla: **v části z AD-User AccountEnabled-Filter users from kosmetice**.
 Změňte hodnotu priority na číslo nižší než 100 (například **90** nebo podle toho, co je nejnižší hodnota dostupná ve vašem prostředí).
 Ujistěte se, že políčka **Povolit synchronizaci hesla** a **zakázáno** nejsou zaškrtnutá.
 Klikněte na **Next** (Další).
  ![Upravit příchozí](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. V **Filtr oboru** klikněte na **Přidat klauzuli**.
 Vyberte **adminDescription** ve sloupci **atribut ve sloupci** operátor a jako hodnotu zadejte **PHSFiltered** .
     ![Filtr oboru](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Nevyžadují se žádné další změny. **Pravidla** a **transformace** spojení by měly být ponechány s výchozím zkopírovaným nastavením, takže můžete kliknout na **Uložit** hned.
 Klikněte na **OK** v dialogovém okně upozornění informující o úplné synchronizaci se spustí v dalším cyklu synchronizace konektoru.
     ![Uložit pravidlo](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Dále vytvořte další vlastní pravidlo s povolenou synchronizací hodnot hash hesel. Pro doménovou strukturu služby Active Directory, pro kterou chcete nakonfigurovat selektivní heslo, vyberte znovu výchozí pravidlo **v části ze služby AD – uživatel AccountEnabled** pro doménovou strukturu Active Directory **, na které** chcete nakonfigurovat selektivní heslo V dalším dialogovém okně vyberte **Ano** , pokud chcete vytvořit upravitelnou kopii původního pravidla.
     ![Vlastní pravidlo](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Zadejte následující název nového vlastního pravidla: **v části od AD-User-AccountEnabled – uživatelé zahrnutí pro kosmetice**.
 Změňte hodnotu priority na číslo nižší než dříve vytvořené pravidlo (v tomto příkladu bude **89**).
 Přesvědčte se, zda je zaškrtnuto políčko **Povolit synchronizaci hesla** a políčko **zakázáno** není zaškrtnuto.
 Klikněte na **Next** (Další).  
     ![Upravit nové pravidlo](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. V **Filtr oboru** klikněte na **Přidat klauzuli**.
 Vyberte **adminDescription** ve sloupci atribut **NOTEQUAL** ve sloupci operátor a jako hodnotu zadejte **PHSFiltered** .
     ![Pravidlo oboru](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Nevyžadují se žádné další změny. **Pravidla** a **transformace** spojení by měly být ponechány s výchozím zkopírovaným nastavením, takže můžete kliknout na **Uložit** hned.
 Klikněte na **OK** v dialogovém okně upozornění informující o úplné synchronizaci se spustí v dalším cyklu synchronizace konektoru.
     ![Pravidla spojování](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Potvrďte vytváření pravidel. Odeberte filtry **Synchronizace hesel** **a** **Typ pravidla** **Standard**. Měli byste vidět i nová pravidla, která jste právě vytvořili.
     ![Potvrdit pravidla](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>Opětovné povolení plánovače synchronizace:  
Po dokončení kroků pro konfiguraci nezbytných synchronizačních pravidel znovu povolte Plánovač synchronizace pomocí následujících kroků:
 1. V prostředí Windows PowerShell spusťte:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Pak ověřte, že bylo úspěšně povoleno spuštěním:

     ```Get-ADSyncScheduler```

Další informace o plánovači najdete v tématu [Azure AD Connect Scheduler Synchronization](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Upravit uživatele **adminDescription** atribut:
Po dokončení všech konfigurací potřebujete upravit atribut **adminDescription** pro všechny uživatele, které chcete **vyloučit** z synchronizace hodnot hash hesel ve službě Active Directory, a přidat řetězec použitý ve filtru Rozsah: **PHSFiltered**.
   
  ![Úprava atributu](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

Pomocí následujícího příkazu PowerShellu můžete také upravit atribut **adminDescription** uživatele:

```Set-ADUser myuser -Replace @{adminDescription="PHSFiltered"}```

## <a name="excluded-users-is-larger-than-included-users"></a>Vyloučení uživatelé je větší než zahrnutí uživatelé.
Následující část popisuje, jak povolit synchronizaci hodnot hash selektivního hesla v případě, že počet uživatelů, kteří mají být **vyloučeni** , je **větší** než počet uživatelů, které chcete **Zahrnout**.

>[!Important]
> Než budete pokračovat, zajistěte, aby byl Plánovač synchronizace zakázán, jak je popsáno výše.

Následuje souhrn akcí, které budou provedeny v následujících krocích:

- Vytvořte upravitelnou kopii **v v rámci služby AD – uživatel AccountEnabled** s možností **Povolit zrušit synchronizaci hodnot hash hesel** a definovat svůj filtr oborů. 
- Vytvořte další upravitelnou kopii výchozího nastavení **v z možnosti AD – uživatel AccountEnabled** s možností **Povolit synchronizaci hodnot hash hesel vybranou** a definovat filtr oboru. 
- Opětovné povolení plánovače synchronizace 
- Nastavte hodnotu atributu ve službě Active Directory, která byla definována jako atribut oboru pro uživatele, které mají být povoleny při synchronizaci hodnot hash hesel. 

>[!Important]
>Kroky pro konfiguraci selektivní synchronizace hodnot hash hesel budou mít vliv jenom na uživatelské objekty, které mají atribut **adminDescription** vyplněný ve službě Active Directory hodnotou **PHSIncluded**.
Pokud tento atribut není naplněný nebo je hodnota jiná než **PHSIncluded** , tato pravidla nebudou použita pro objekty uživatele.


### <a name="configure-the-necessary-synchronization-rules"></a>Nakonfigurujte nezbytná pravidla synchronizace:

 1. Spusťte Editor pravidel synchronizace a nastavte **synchronizaci hesla** filtrů **na** **úrovni Standard** a **typu pravidla** .
     ![Typ pravidla](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Vyberte pravidlo **ze služby AD – uživatel AccountEnabled** pro doménovou strukturu služby Active Directory, pro kterou chcete nakonfigurovat selektivní heslo, a klikněte na **Upravit**. V dalším dialogovém okně vyberte **Ano** , pokud chcete vytvořit upravitelnou kopii původního pravidla.
     ![V nástroji ze služby AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. První pravidlo zakáže synchronizaci hodnot hash hesel. Zadejte následující název nového vlastního pravidla: **v části z AD-User AccountEnabled-Filter users from kosmetice**.
 Změňte hodnotu priority na číslo nižší než 100 (například **90** nebo podle toho, co je nejnižší hodnota dostupná ve vašem prostředí).
 Ujistěte se, že políčka **Povolit synchronizaci hesla** a **zakázáno** nejsou zaškrtnutá.
 Klikněte na **Next** (Další).
  ![Nastavit prioritu](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. V **Filtr oboru** klikněte na **Přidat klauzuli**.
Vyberte **adminDescription** ve sloupci atribut **NOTEQUAL** ve sloupci operátor a jako hodnotu zadejte **PHSIncluded** .
     ![Přidat klauzuli](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Nevyžadují se žádné další změny. **Pravidla** a **transformace** spojení by měly být ponechány s výchozím zkopírovaným nastavením, takže můžete kliknout na **Uložit** hned.
 Klikněte na **OK** v dialogovém okně upozornění informující o úplné synchronizaci se spustí v dalším cyklu synchronizace konektoru.
     ![Transformace](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Dále vytvořte další vlastní pravidlo s povolenou synchronizací hodnot hash hesel. Pro doménovou strukturu služby Active Directory, pro kterou chcete nakonfigurovat selektivní heslo, vyberte znovu výchozí pravidlo **v části ze služby AD – uživatel AccountEnabled** pro doménovou strukturu Active Directory **, na které** chcete nakonfigurovat selektivní heslo V dalším dialogovém okně vyberte **Ano** , pokud chcete vytvořit upravitelnou kopii původního pravidla.
     ![AccountEnabled uživatele](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Zadejte následující název nového vlastního pravidla: **v části od AD-User-AccountEnabled – uživatelé zahrnutí pro kosmetice**.
 Změňte hodnotu priority na číslo nižší než dříve vytvořené pravidlo (v tomto příkladu bude **89**).
 Přesvědčte se, zda je zaškrtnuto políčko **Povolit synchronizaci hesla** a políčko **zakázáno** není zaškrtnuto.
 Klikněte na **Next** (Další).
     ![Povolit synchronizaci hesel](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. V **Filtr oboru** klikněte na **Přidat klauzuli**.
 Vyberte **adminDescription** ve sloupci **atribut ve sloupci** operátor a jako hodnotu zadejte **PHSIncluded** .
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Nevyžadují se žádné další změny. **Pravidla** a **transformace** spojení by měly být ponechány s výchozím zkopírovaným nastavením, takže můžete kliknout na **Uložit** hned.
 Klikněte na **OK** v dialogovém okně upozornění informující o úplné synchronizaci se spustí v dalším cyklu synchronizace konektoru.
     ![Uložit hned](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Potvrďte vytváření pravidel. Odeberte filtry **Synchronizace hesel** **a** **Typ pravidla** **Standard**. Měli byste vidět i nová pravidla, která jste právě vytvořili.
     ![Synchronizovat na](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Opětovné povolení plánovače synchronizace:  
Po dokončení kroků pro konfiguraci nezbytných synchronizačních pravidel znovu povolte Plánovač synchronizace pomocí následujících kroků:
 1. V prostředí Windows PowerShell spusťte:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Pak ověřte, že bylo úspěšně povoleno spuštěním:

     ```Get-ADSyncScheduler```

Další informace o plánovači najdete v tématu [Azure AD Connect Scheduler Synchronization](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Upravit uživatele **adminDescription** atribut:
Po dokončení všech konfigurací potřebujete upravit atribut **adminDescription** pro všechny uživatele, které chcete **Zahrnout** do synchronizace hodnot hash hesel ve službě Active Directory, a přidat řetězec použitý ve filtru Rozsah: **PHSIncluded**.

  ![Upravit atributy](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 Pomocí následujícího příkazu PowerShellu můžete také upravit atribut **adminDescription** uživatele:

 ```Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}``` 

## <a name="next-steps"></a>Další kroky
- [Co je synchronizace hodnot hash hesel?](whatis-phs.md)
- [Jak funguje synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)

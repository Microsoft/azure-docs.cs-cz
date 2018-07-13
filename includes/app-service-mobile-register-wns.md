
1. V Průzkumníku řešení Visual Studio klikněte pravým tlačítkem na projekt aplikace Windows Store. Potom vyberte **Store** > **přidružit aplikaci Store**.

    ![Propojit aplikaci s Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. V průvodci vyberte **Další**. Pak se přihlaste pomocí svého účtu Microsoft. V **rezervovat nový název aplikace**, zadejte název pro vaši aplikaci a pak vyberte **rezervy**.
3. Po registraci aplikace je úspěšně vytvořen, vyberte nový název aplikace. Vyberte **Další**a pak vyberte **přidružit**. Tento postup přidá požadované informace o registraci Windows Store do manifestu aplikace.
4. Opakujte kroky 1 až 3 pro projekt aplikace Windows Phone Store s použitím stejného registrace, který jste dříve vytvořili pro aplikace Windows Store.  
5. Přejděte [Windows Dev Center](https://dev.windows.com/en-us/overview)a pak se přihlaste pomocí svého účtu Microsoft. V **Moje aplikace**, vyberte možnost registrace nové aplikace. Pak rozbalte **služby** > **nabízená oznámení**.
6. Na **nabízená oznámení** stránce v části **Windows Push Notification Services (WNS) a Microsoft Azure Mobile Apps**vyberte **Web služeb Live Services**.  Poznamenejte si hodnoty **SID balíčku** a *aktuální* hodnota v **tajný klíč aplikace**. 

    ![Nastavení aplikace v Centru pro vývojáře](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Tajný klíč aplikace a SID balíčku jsou důležité přihlašovací údaje zabezpečení. Nemáte s kýmkoli sdílet tyto hodnoty ani je nedistribuujte s vaší aplikací.
   >
   >

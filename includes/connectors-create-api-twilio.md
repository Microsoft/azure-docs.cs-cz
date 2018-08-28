### <a name="prerequisites"></a>Požadavky
* Účtu Twilio
* Ověřené telefonní číslo Twilio, které můžou přijímat zprávy SMS
* Ověřené telefonní číslo Twilio, který můžete poslat SMS

> [!NOTE]
> Pokud používáte zkušební verzi účtu Twilio, lze provést odeslání pouze odeslání SMS **ověřit** telefonních čísel.  
> 
> 

Před použitím svého účtu Twilio v aplikaci logiky, musí autorizovat aplikaci logiky se připojit ke svému účtu Twilio. Naštěstí vám pomůžou snadno z v rámci aplikace logiky na webu Azure Portal. 

Tady jsou kroky k autorizaci aplikace logiky se připojit ke svému účtu Twilio:

1. Chcete-li vytvořit připojení k Twiliu, v návrháři aplikace logiky, vyberte **rozhraní API spravovaná Microsoftem zobrazit** v rozevíracím seznamu zadejte *Twilio* do vyhledávacího pole. Výběr triggeru nebo akce, které budete chtít použít:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Pokud jste ještě nevytvořili žádná připojení k Twiliu před, bude výzva k zadání přihlašovacích údajů Twilio. Tyto přihlašovací údaje se použijí k autorizaci aplikace logiky pro připojení k a přístup k datům vašeho účtu Twilio:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Budete potřebovat **id účtu Twilio** a **přístupovým tokenem Twilio** z řídicího panelu Twilio, takže přihlásit ke svému účtu Twilio a zkopírovat tyto dva druhy údajů:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio a Logic apps použít jiné názvy pro identifikaci tyto dva druhy údajů. Zde je, jak je nutné mapovat do dialogového okna aplikace logiky: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Vyberte **vytvořit připojení** tlačítka:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Všimněte si, připojení bylo vytvořeno a teď jste zdarma postupujte podle dalších kroků ve vaší aplikaci logiky:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)


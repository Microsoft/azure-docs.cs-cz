### <a name="prerequisites"></a>Požadavky
* A [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) účtu  

Předtím, než v aplikaci logiky můžete používat svůj účet SMTP, musíte se autorizovat aplikaci logiky se připojit ke svému účtu SMTP. Naštěstí vám pomůžou snadno z v rámci aplikace logiky na webu Azure Portal.  

Tady jsou kroky k autorizaci aplikace logiky se připojit ke svému účtu SMTP:  

1. Chcete-li vytvořit připojení k SMTP serveru, v návrháři aplikace logiky, vyberte **rozhraní API spravovaná Microsoftem zobrazit** v rozevíracím seznamu zadejte *SMTP* do vyhledávacího pole. Výběr triggeru nebo akce, které budete chtít použít:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Pokud jste ještě nevytvořili žádná připojení k SMTP serveru před, zobrazí výzva k zadání přihlašovacích údajů SMTP. Tyto přihlašovací údaje se použijí k autorizaci aplikace logiky pro připojení k a přístup k datům účet SMTP:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Všimněte si, připojení bylo vytvořeno a teď jste zdarma postupujte podle dalších kroků ve vaší aplikaci logiky:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  


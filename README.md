# MaterialFulfillment
first notes (july 6 2017 Carl Mattocks, Paul Alagna)
KEY
<<person>>
[ multiple ]
// process //
PAGE 1
<<buyer>> signon
- screen buyer-signon (user-name, password, date) ==> Function password () ==> BuyerSessionID OR function PaswordFail ()
buyer request of service
- screen buyer-request ( 
    - cascading lists ( 
          catagory-of-services** -> services-list (( 
                                       service-guid , serviceDescription service-score**, service-gps-area
                                                   )) - 
                       )
    - buyer-gps { OR zip code }
    - hidden ( buyerSessionID )
    ON Commit
     - function heartBeat () ==> HeartBeatID OR TimePassedError
     - function EmailAckToBuyer ()
     - function writeXAQ ((XRQguid) )
     
   ** service-score - orders to orders completed satisfactorily ratio
   ** catagory-of-services loaded from catagoryOfServicesPool
   
   PAGE 2
   ServaceXAQ {description, [gps] } ==> <<Concierge>> ==> CatagoryDB
   <<Concierge>> via [[chatbot]] to <<service>> edits CatagoryDB
   
   CatagoryDB
   ServiceProfile --< catagoryOfService >-- CatagoryOfServicesPool
   
   PAGE 3
   Service signon
   - screen Service-signon (userName, Password, date) ==> Function password () ==> ServiceSessionID OR function PaswordFail ()
   
   Service creation of offering
   - screen ServiceOffering (
       service UserName
       gps limits of service ( or ZIP codes )
       
       [ description of service , price of offering , time limit ] 
       - hidden ( ServiceSessionID )
       ) On Commit:
          - function heartBeat () ==> HeartBeatID OR TimePassedError
          - assign OfferingGuid via function GetGuid()
          - function EmailAckToService ()
          - function writeServiceXAQ ((OfferingGuid )
          
PAGE 4
Service Profile
- screen 
    service Provider (
        - userName ** unique in table
        - service provider name
        - address
        - service address
        - service emailAddress
        - service WebURL
        - [ telephone numbers ]
        - Chamber of commerce ID
        - Dunns code
        - IRS TIN
        - year started
        - photo of logo
        - [ gps range of service ]
        ) On Commit
            - function heartBeat () ==> HeartBeatID OR TimePassedError
            - assign ServiceGuid via function GetGuid()
            - function EmailAckToService ('profile')
            - function writeServiceProfile ((ServiceGuid )
            
 
Page 5
per XAQ(i) 
- verify BuyerGPS is within serviceGPS
   - //formGen()//==> statmentOfWork
   -- statmentOfWorkRecord (
    service ServiceGuid, address, description of service, price , time limit
    buyer   BuyerID      address 
    statement-date
    )
    
Page 6
per StatementOfWork(i)
    - add terms of usage ** tbd 
    ==> <<risk&Liability>>
        - assign agreementID via getGuid()
        ==> formated agreementRecord


Page 7
per AgreementRecord(i)
    - email to Buyer with link to BuyerContractScreen
    - begin WatchDogTimer (WDT(7Day, 'buyerSignage',date, BuyerGuid)
    

Page 8
<<buyer>> via link to BuyerContractScreen
screen BuyerContractScreen (
        agreement id
        service name, address, email
        buyer name, address, email
        offering description, price, timeLimit
        radio-pair (do you agree that work should be performed, Cancel this offering)
        hidden BuyerSessionID
        ) On Commit
            - function heartBeat () ==> HeartBeatID OR TimePassedError
            - function EmailAckToBuyer ('BuyerContract')
            - function write ASignedAgreementRecord ((agreementID )

Page 9
per ASignedAgreementRecord(i)
    - email to Service with link to ServiceContractScreen
screen ServiceContractScreen (
    agreement id
        service name, address, email
        buyer name, address, email
        offering description, price, timeLimit
        radio-pair (do you agree to perform work, Cancel this offering)
        hidden ServiceSessionID
    ) On Commit
          - function heartBeat () ==> HeartBeatID OR TimePassedError
          - function EmailAckToService('ServiceContract')
          - assign ContractID via getGuid()
          - begin WDT(14Day, date, "BeginContract", contractID, ServiceID, BuyerID )
          - begin WDT(28Day, date, "ContractBreakDown", contractID, ServiceID, BuyerID )
          - function write ContractRecord ((agreementID )

Page 10
5 conditions
- buyer responds first
- buyer responds second
- service responds first
- service responds second
- WDT  contract breakdown

Page 11
- buyer responds first
screen BuyerResponseScreen
         (
           buyer name, address, email
           contract# { also may be filled by sub screen }
           ((serviceProviderSubScreen))
           radio-pair ( "are you satisfied?", Not satisfied )
           hidden BuyerSessionID
         ) On commit
             - function heartBeat () ==> HeartBeatID OR TimePassedError
             - function EmailAckToBuyer('ContractFulfilment')
             - function write contractFulfillmentRecord
             
screen serviceProviderSubScreen 
         (
           servive name, address, webURL
           offering date of service
         ) on Commit
            - return Contract#
         
Page 12
- buyer responds second

Page 13
- service responds first

Page 14
- service responds second

Page 15
- WDT  contract breakdown

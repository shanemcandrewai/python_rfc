# Connect to SAP NetWeaver RFC
## [SAP Note 2573790](https://launchpad.support.sap.com/#/notes/2573790) - Installation, Support and Availability of the SAP NetWeaver RFC Library 7.50
### Download installation files for the [SAP NW RFC SDK](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT)
#### Add the subdirectory "lib" of the SDK to the operating system's library path environment variable
##### Example
###### Windows
    C:\Users\shane\dev\nwrfcsdk\lib
#### Set SAPNWRFC_HOME environment variable to that location
##### Example
###### Windows
    C:\Users\shane\dev\nwrfcsdk\
### On Windows, the [Microsoft C Runtime DLLs version 12.0](https://support.microsoft.com/en-us/help/4032938) (Visual C++ 2013) are required.
### [PyRFC](https://github.com/SAP/PyRFC)
#### [Upgrade pip](https://pip.pypa.io/en/stable/installing/#upgrading-pip)
    python -m pip install -U pip
#### [Download and installation PyRFC](https://github.com/SAP/PyRFC#download-and-installation)
    pip install pyrfc
## Example Usage
### Intialization
    from pyrfc import Connection
    abap_system = {
      'user'      : 'ZMCANSH1',
      'passwd'    : 'xxx',
      'ashost'    : 'ITBOLTE4AS01',
      'sysnr'     : '00',
      'client'    : '310',
      'lang'      : 'EN'
      }
    conn = Connection(**abap_system)
#### Select from a single table	
    conn.call('/SAPDS/RFC_READ_TABLE', QUERY_TABLE = 'T000',
	    DELIMITER = ',', OPTIONS = ['MANDT EQ 200'])['DATA']
#### Execute ABAP statements from a file
    with open('[ZEWM_INCONSISTANT_ROUTES.abap](ZEWM_INCONSISTANT_ROUTES.abap)') as f:
        prog = f.read().splitlines()
    conn.call('/SAPDS/RFC_ABAP_INSTALL_RUN', PROGRAM=prog)['WRITES']

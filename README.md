# DICOM Printer 2 Config Bundle
### PDF Text-to-DICOM Extraction

This is a configuration bundle that contains:

- A link to download a revised version of DICOM Printer 2 Drop Monitor,
- A sample DICOM Printer 2 `config.xml` that can be used for PDF meta-data extraction, and
- This guide.

## Purpose

Sometimes PDFs contain a sequence of meta-attributes.  These attributes, which act kind 
of like the equivalent of a DICOM header, can be used to pass information from PDF to 
DICOM Printer 2 engine for use with query and storage.

This is a very brief guide for how to deploy this configuration on top of a regular DICOM
Printer 2 install.

## Installation

### Install the Binaries

- Download and install DICOM Printer 2 from the Flux Software website at:  
  https://store.fluxinc.ca/files/DP2/latest
  
  You can skip the wizard, and leave the configuration as-is.
  
- Download and unzip the contents of the following zip to `Program Files\Flux Inc\DICOM Printer 2`  
  https://www.fluxinc.ca/wp-content/uploads/2020/08/DICOMPrinterDropMonitorService-2020-08-05.zip

- Copy `config.xml` from **this repo** to `ProgramData\Flux Inc\DICOM Printer 2\config

### Replace Drop Monitor Service

Since this is a replacement drop folder monitor, you need to stop and disable the old one:

- Open up the Service Manager (Start -> Run - `services.msc`)
- Find the **DICOM Printer 2 Drop Monitor** service, right-click and stop it, and then double-click and Disable it.
- Drop into an administrative shell and install the new service

```
$ cd \Program Files\Flux Inc\DICOM Printer 2
$ DICOMPrinterDropMonitorService --install
```

You will see a bit of a wall of text, and some indication of success.

Note that this process creates a *second* service in your Service Manager.  You will now see both the original
that you previously disabled, and this newly-installed instance.  Be sure to only use the new instance when starting
or stopping the monitor.

## Configuration

The sample config.xml file in this repository defines the drop location at `C:\ProgramData\Flux Inc\DICOM Printer 2\drop`.  
Please make sure this folder exists before you start the service and make sure to re-start the service after any change to this location.

Next, also in `config.xml`

- Edit the PACS information included in the `<Store>` element.  This includes the IP address, 
AE title, and port of the destination PACS.

```xml
<Store name="Store">
  <Compression type="RLE" />
  <ConnectionParameters>
    <MyAeTitle>DICOM_PRINTER</MyAeTitle>
    <PeerAeTitle>CONQUESTSRV1</PeerAeTitle>
    <Host>localhost</Host>
    <Port>5678</Port>
  </ConnectionParameters>
</Store>
```
- Save the file and (re)start the `DICOM Printer 2` service.
- Tail the log at `ProgramData\Flux Inc\DICOM Printer 2\logs` to see what's going on.

### Drop and Parse a PDF

1. Start the Drop Monitor service
2. Start the DICOM Printer 2 service
3.  Drop a PDF into `C:\ProgramData\Flux Inc\DICOM Printer 2\drop`, or wherever you defined 
the drop location.

In your logs you should see indication of successful attribute extraction and then
a storage attempt.

### IMPORTANT

This configuartion leaves DP2 in a very chatty state.  It's best to reduce the `Verbosity` attribute to
something like 2 or 3 once you're happy with operation.



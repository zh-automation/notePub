---
title: PLC Basics
draft: false
tags: - PLC
--- 

# Why is it a PLC?
Why isn't it a PC? It is just a computer. A PLC (Programmable Logic Controller) has all the same basic components: CPU, RAM, storage, etc. So why can't a PC be a PLC? Well, it can and it often is, but even the PCs in industrial environments are considered separate from their peers. They're usually labeled IPCs (Industrial Personal Computer... not a great name) and, like PLCs they are usually differentiated in two ways.

### Certified Rugged
One of the many things that industrial component manufacturers have to deal with is getting certified in specific environments. IPCs and PLCs are no different. They have to be able to run in environments that most laptops would not do well in. I've seen PLCs working in rooms exposed to chlorine, corroding all their terminals and wreaking havoc on their shells. Granted, even those PLCs weren't designed for that, but they kept on trucking. To prove to prospective buyers that their equipment can survive in rough environments, many manufacturers spend millions on getting their equipment certified with UL, ABS, CE and any communication protocols they may want to claim compliance with (Ethernet/IP, Modbus, Profinet, EtherCAT, etc.). 

### Real Time Processing
PLCs have a processor (or sometimes a microcontroller) usually running an RTOS (Real Time Operating System), that is, a scan level, deterministic processing system that allows control over the execution of commands at set rates, sequences and response times. This allows PLCs to have a set cadence that the programmer can rely on so they know when and how their programs will execute, and in what order. 
This differs greatly from your standard Windows or Linux box that will execute everything as fast as it can and in whatever order makes sense (or it has resources for) based on priority and dependency. The asynchronistic and non-deterministic nature of end-user OSs doesn't work well in the industrial environment where x *always needs to precede* y, no matter what.
IPCs run Linux and Windows, but with a huge caveat. When the PLC software is installed they patch the kernel (the interface between the OS and the hardware) that creates the same real-time deterministic environment for the PLC code to run on exclusively. They often set aside CPU cores specifically for the PLC code to run on, essentially carving out the resources needed for a PLC within the OS.
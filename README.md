# Nagios-plugin-requesttracker-ticketcount

## Overview


A Nagios plugin to check Request Tracker for the number of tickets that satisfy some arbitrary TicketSQL.

This is a simple Nagios plugin to monitor your Request Tracker queues. Pass it valid TicketSQL and it will check how many tickets satisfy the conditions. Nagios will then alert based on your thresholds. The initial use case was for "I want to know if there more than N unopened tickets in this queue because it means I need more agents working that queue".

## Installation

In your Nagios plugins directory, run

<pre><code>git clone git://github.com/dhutty/nagios-plugin-requesttracker.git</code></pre>

Your nagios plugins directory is likely /usr/lib64/nagios/plugins or /usr/lib/nagios/plugins if you installed your distribution's nagios packages.

At some point, it would be nice to have rpm/deb packaging for this plugin.

## Usage

### Permissions

Create a RT user with sufficient (readonly?) permissions to perform the queries that you have in mind.

Create a config file that contains that user's credentials, this is the file that is referred to by the plugin's -f option.
<pre><code>
user = new_user
pass = new_pass
</code></pre>


### Nagios configuration to utilise it

Edit your commands.cfg and add the following
<pre><code>
define command{
        command_name    check_rt_ticketcount
        command_line    $USER1$/check_rt_ticketcount -f /etc/nagios/rtrc -u "$ARG1$" --query '$ARG2$' -w $ARG3$ -c $ARG4$
}    
</code></pre>

Edit your services.cfg and add the following
<pre><code>
define service {
  use                            generic-service
  service_description            check-work-queue
  host_name                      rt.example.com
  check_command                  check_rt_ticketcount!https://rt.example.com!queue ="work_queue" and (status="open" or status="new") and created < "5 minutes ago"!2!5
}
</code></pre>

A Nagios plugin for [Request Tracker](http://bestpractical.com/rt) that will check the number of tickets that satisfy some arbitrary TicketSQL

This requires a few perl non-core perl modules: RT::Client::REST, Nagios::Plugin and Config::Tiny, some of which themselves have some dependencies.

## Author

  Duncan Hutty dhutty@allgoodbits.org



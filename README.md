
# Mojo::Pg [![Build Status](https://travis-ci.org/kraih/mojo-pg.svg?branch=master)](https://travis-ci.org/kraih/mojo-pg)

  A tiny wrapper around [DBD::Pg](https://metacpan.org/pod/DBD::Pg) that makes
  [PostgreSQL](http://www.postgresql.org) a lot of fun to use with the
  [Mojolicious](http://mojolicious.org) real-time web framework.

```perl
use Mojolicious::Lite -signatures;
use Mojo::Pg;

helper pg => sub { state $pg = Mojo::Pg->new('postgresql://postgres@/test') };

# Use migrations to create a table during startup
app->pg->migrations->from_data->migrate;

get '/' => sub ($c) {

  my $db = $c->pg->db;
  my $ip = $c->tx->remote_address;

  # Store information about current visitor blocking
  $db->query('insert into visitors values (now(), ?)', $ip);

  # Retrieve information about previous visitors non-blocking
  $db->query('select * from visitors limit 50' => sub ($db, $err, $results) {

    return $c->reply->exception($err) if $err;

    $c->render(json => $results->hashes->to_array);
  });
};

app->start;
__DATA__

@@ migrations
-- 1 up
create table visitors (at timestamp with time zone, ip text);
-- 1 down
drop table visitors;
```

## Installation

  All you need is a one-liner, it takes less than a minute.

    $ curl -L https://cpanmin.us | perl - -M https://cpan.metacpan.org -n Mojo::Pg

  We recommend the use of a [Perlbrew](http://perlbrew.pl) environment.

## Want to know more?

  Take a look at our excellent
  [documentation](http://mojolicious.org/perldoc/Mojo/Pg)!

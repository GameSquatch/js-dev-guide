# Server Configuration

Since Convergence is based on the [Akka Framework](https://akka.io/), it uses the [Lightbend Config](https://github.com/lightbend/config) framework to configure the server. Please reference the Lightbend Config documentation for the specific syntax for configuration.

## Reference Configuration
The [Reference Configuration](https://github.com/convergencelabs/convergence-server/blob/master/src/main/resources/reference.conf) can be found on GitHub and provides a good amount of documentation on the configuration.  It is also shown below.  If there are any discrepancies, the GitHub version should take precedence.

```text
#####################################
# Convergence Reference Config File #
#####################################

# This reference file contains all of the default settings for Convergence.
# The file contains both defaults for Convergence itself, as well as for
# Akka. These settings can be overridden in the convergence-server.conf file.

# The root of all Convergence specific configurations.
convergence {

  # Determines how long the server will wait for start up to complete before
  # assuming there is a failure.
  server-startup-timeout = 5 minutes

  # Determines how long the server will wait for shut down to complete before
  # assuming there is a failure.
  server-shutdown-timeout = 2 minute

  # Determines how long the server will wait for the Akka ActorSystem to
  # gracefully terminate before exiting the JVM.
  system-termination-timeout = 30 seconds

  # How long a Domain will remain active after the last client disconnects.
  domain-shutdown-delay = 10 minutes

  # The number of shards for the Akka Cluster Sharding for the various
  # Shard region Convergence uses.
  shard-count = 100

  # Persistence related configurations.
  persistence {

    # Configurations for the Orient DB Database.
    server {
      # The connection URI for the database. This MUST be set in the
      # convergence-server.conf.
      uri = ""

      # The username of a user that has permissions to create and drop
      # databases, change schema, and manage users.
      admin-username = "root"

      # The password for the admin user.
      admin-password = "password"
    }

    # Configurations for the Convergence database within OrientDB.
    convergence-database {

      # The name of the Convergence database.
      database = "convergence"

      # Note: Orient DB uses default username and passwords for the databases.
      # these will be deleted and the following users will be created.

      # The username of a user that has write privileges to the database.
      username = writer

      # The password of a user that has write privileges to the database.
      password = writer

      # The password of a user that has the ability to create schema within
      # the Convergence database. This user will be used to install and
      # upgrade schema.
      admin-username = admin

      # The password for the admin database user.
      admin-password = admin

      # Determines the behavior of the Convergence database auto-installation
      # feature. The server can check to see if the Convergence database has
      # been installed on startup and if not, automatically install it.
      auto-install = {
        # Whether the auto-install is enabled or not.
        enabled = true

        # If the schema to be installed should include pre-release (e.g.
        # development) deltas. Default is false for production environments.
        pre-release = false
      }

      # When starting up it is possible that the Database has not yet started.
      # This configures the retry interval between connection attempts.
      retry-delay = 15s

      # The timeout to wait for the Convergence database to become initialized.
      # Either because it needed to be installed, or just to get the answer
      # from the system that it is already initialized.
      initialization-timeout = 10m

      # Configures the OrientDB Connection pool.
      pool {
        # The minimum number of connections to keep alive.
        db-pool-min = 0

        # The maximum number of connections to create.
        db-pool-max = 100
      }
    }

    # Configurations for the Domain specific databases that Convergence
    # will create for each Domain.
    domain-databases {
      # Whether to use the default Orient DB usernames and passwords for
      # domain database. This should be set to true for production. It
      # may be useful to set it to false in a development mode in order to
      # more easily log into the Domain databases.
      randomize-credentials = true

      # If the schema to be installed should include pre-release (e.g.
      # development) deltas. Default is false for production environments.
      pre-release = false

      # The maximum amount of time to wait for a Domain database to be
      # initialized before assuming initialization has failed.
      initialization-timeout = 5m

      # The maximum amount of time to wait for a Domain database to be
      # removed.
      deletion-timeout = 5m

      # Configures the OrientDB Connection pool.
      pool {
        # The minimum number of connections to keep alive.
        db-pool-min = 0

        # The maximum number of connections to create.
        db-pool-max = 100
      }
    }
  }

  # Configures how the Server will be initialized on first startup.
  bootstrap {

    # Default configurations to add to the Convergence database.
    default-configs {
      namespaces.enabled = true
      namespaces.user-namespaces-enabled = true
      namespaces.default-namespace = convergence
    }

    # A list of namespaces to create in the server.
    namespaces = [{
      id = convergence
      displayName = "Convergence"
    }]

    # The set of domains to create.
    domains = [{
      namespace: "convergence"
      id: "default"
      displayName: "Default"
      favorite: true
      config {
        anonymousAuthEnabled: false
      }
    }]
  }

  # Configures the default server admin user. This user will always exist in
  # the system. If it is deleted, it will be re-created on server restart.
  # This ensures there is always a way to administer the server.
  default-server-admin {
    # The username of the default server admin.
    username = admin

    # The password of the default server admin.
    password = password

    # The email of the default server admin.
    email = "admin@example.com"

    # The first name of the default server admin.
    firstName = Server

    # The last name of the default server admin.
    lastName = Admin

    # The display name of the default server admin.
    displayName = Server Admin
  }

  # Configurations for the Convergence user manager.
  user-manager {
    # A timeout to wait for domains in a user's namespace to be deleted when
    # deleting the user.
    domain-deletion-timeout = 10s
  }

  # Configurations for the Realtime API subsystem.
  realtime {
    # The host name to bind to. This defaults to "0.0.0.0" which indicates the
    # server should bind to all available interfaces.
    host = "0.0.0.0"

    # The TCP port to bind to.
    port = 8080

    # Configurations for the WebSocket server.
    websocket {
      # The maximum number of frames to buffer for an individual web socket
      # message.
      max-frames = 1024

      # The maximum amount of time to wait for the stream of frames for a
      # single message to be completed.
      max-stream-duration = 10s
    }

    # Configurations for the Convergence Realtime protocol.
    protocol {
      # How long to wait for the handshake process between the client and
      # server to complete.
      handshake-timeout = 10s

      # How long the server should wait to complete processing a client request
      # before sending a timeout back to the client.
      default-request-timeout = 20s

      # Configures the keep-alive / failure detection heart beat protocol. This
      # feature helps keep the web socket connection alive and also allows both
      # the client and server to more readily detect when the other side has
      # disconnected uncleanly.
      heartbeat = {
        # Whether the heartbeat is enabled or not.
        enabled = true

        # How long to wait after receiving the last message from the client
        # to send the client a ping message.
        ping-interval = 10s

        # How long to wait for a pong (or any message) from the client after a
        # ping before determining the client has disconnected.
        pong-timeout = 10s
      }
    }

    # Configurations for the ClientActor on the server. Each connected client
    # has a corresponding ClientActor on the server that manages the session
    # with that client.
    client {
      # How long the WebSocket service should wait for the ClientActor to be
      # spawned. The client actor is always spawned on the same host that the
      # web socket connection came in on, so this should be a quick operation.
      client-creation-timeout = 2s

      # The maximum amount of time the client should wait to resolve the
      # identity of users mentioned in outgoing messages in order to send
      # identity cache updates to the client.
      identity-resolution-timeout = 3s

      # How long the client actor should wait to handshake with the DomainActor
      # in the backend.
      domain-handshake-timeout = 5s

      # How long the client actor should wait for the authentication process
      # to complete.
      domain-authentication-timeout = 5s

      # How long the client should wait to resolve the connecting users
      # presence information needed to respond to the authentication
      # request.
      auth-presence-timeout = 3s

      # How often the client should send a heartbeat to the domain to
      # ensure the session is kept alive
      heartbeat-interval = 30s
    }

    # Configurations for the Realtime Model subsystem.
    model {
      # How long a RealtimeModelActor should remain in memory after the last
      # client disconnected and/or the last REST request was received.
      passivation-timeout = 10s

      # How long to wait for a client to provide model data during model
      # initialization.
      client-data-timeout = 15s

      # How long to wait for a client that is resynchronizing a model to
      # either send the next operation, or to complete the resync process
      # after the server has indicated that it has sent all of the
      # operations.
      resynchronization-timeout = 5m
    }

    # Configurations for the chat subsystem.
    chat {
      # How long a chat actor should remain in memory after the last message /
      # event was received. This does not apply to Chat Rooms since chat rooms
      # shut down as soon as now clients are connected.
      passivation-timeout = 120s
    }

    activity {
      # How long an activity actor should remain in memory after the last message /
      # event was received when no participants are joined to the activity.
      passivation-timeout = 10s
    }

    # Configurations for the Domain Actors
    domain {
      # How long a Domain Actor will stay in memory after the last client
      # disconnected.
      passivation-timeout = 10s

      # How often the domain actor should check for sessions that haven't
      # sent a heart beat within the specified duration.
      session-prune-interval = 2m

      # How long beyond the last heartbeat should the domain wait before
      # considering the session to be disconnected
      idle-session-timeout = 1m
    }
  }

  # Configurations for the REST API subsystem.
  rest {
    # The host name to bind to. This defaults to "0.0.0.0" which indicates the
    # server should bind to all available interfaces.
    host = "0.0.0.0"

    # The TCP port to bind to.
    port = 8081

    # How long the DomainRestActor will stay in memory after the last REST
    # request was received.
    rest-domain-shutdown-delay = 2 minutes

    # The maximum amount of time it should take for a DomainRestActor to
    # shut down.
    max-rest-actor-shutdown = 30 seconds
  }

  # Configures the offline subsystem.
  offline {
    # Specifies the interval between checking for model updates.
    model-sync-interval = 5 minutes
  }
}

```




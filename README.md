CREATE TABLE raw_telegrams (
	raw_id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
	center VARCHAR(255),
	shr_raw_te TEXT,
	dep_raw_text TEXT,
	arr_raw_text TEXT,
	file_name VARCHAR(255),
	processed_at TIMESTAMP WITH TIME ZONE,
	processing_status VARCHAR(50)
);

CREATE INDEX raw_telegrams_raw_id_IDX ON raw_telegrams (raw_id);
CREATE INDEX region_raw_id_IDX ON region (raw_id);



CREATE TABLE region (
	region_id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
	region_name VARCHAR(250),
	raw_id INTEGER NOT NULL,
	geometry GEOMETRY,
	"area_km2(double precision)" DOUBLE PRECISION,
	departure_time TIME,
	arrival_time TIME,
	CONSTRAINT region_raw_telegrams_FK FOREIGN KEY (raw_id) REFERENCES raw_telegrams(raw_id)
);



CREATE TABLE Flight (
	flight_id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
	drone_id INTEGER NOT NULL,
	report_flight_id INTEGER NOT NULL,
	raw_id INTEGER NOT NULL,
	region_id INTEGER NOT NULL,
	flight_date DATE NOT NULL,
	drone_type VARCHAR DEFAULT ('100'),
	duration_minutes INTEGER NOT NULL,
	departure_coords VARCHAR DEFAULT ('100') NOT NULL,
	arrival_coords VARCHAR(100) NOT NULL,
	departure_point POINT,
	arrival_point POINT,
	metadata JSONB,
	departure_region_id INTEGER,
	arrival_region_id INTEGER,
	created_at timestamp with time zone NOT NULL,
	CONSTRAINT Flight_raw_telegrams_FK FOREIGN KEY (raw_id) REFERENCES raw_telegrams(raw_id),
	CONSTRAINT Flight_region_FK FOREIGN KEY (region_id) REFERENCES region(region_id),
	CONSTRAINT Flight_report_flight_FK FOREIGN KEY (report_flight_id) REFERENCES report_flight(report_flight_id)
);

CREATE INDEX Flight_raw_id_IDX ON Flight (raw_id,region_id,report_flight_id);


CREATE TABLE report_flight (
	report_flight_id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
	date DATE NOT NULL,
	period DATE NOT NULL,
	report_id INTEGER NOT NULL,
	fliht_id INTEGER NOT NULL
, drone_id INTEGER);

CREATE UNIQUE INDEX report_flight_report_id_IDX ON report_flight (report_id);
CREATE UNIQUE INDEX report_flight_fliht_id_IDX ON report_flight (fliht_id);














CREATE TABLE report_log (
	report_id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
	date DATE NOT NULL,
	period DATE NOT NULL,
	user_id INTEGER NOT NULL,
	CONSTRAINT report_log_report_flight_FK FOREIGN KEY (report_id) REFERENCES report_flight(report_flight_id),
	CONSTRAINT report_log_user_FK FOREIGN KEY (user_id) REFERENCES "user"(user_id)
);

CREATE INDEX report_log_user_id_IDX ON report_log (user_id,report_id);





CREATE TABLE "user" (
	user_id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
	"role" VARCHAR DEFAULT ('50') NOT NULL
);

CREATE INDEX user_user_id_IDX ON "user" (user_id);

